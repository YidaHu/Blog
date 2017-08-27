---
title: Android自动更新实现
date: 2017-07-15 09:40:57
categories: Android
tags:
- 自动更新
---

![391H](android-autoupdate\391H.jpg)

# 1.前言

一个好的应用软件都是需要好的维护，从初出版本到最后精品，这个过程需要版本不停的更新，那么如何让用户第一时间获取最新的应用安装包呢？那么就要求我们从第一个版本就要实现升级模块这一功能。

自动更新功能的实现原理，就是我们事先和后台协商好一个接口，我们在应用的主Activity里，去访问这个接口，如果需要更新，后台会返回一些数据(比如，提示语；最新版本的url等)。然后我们给出提示框，用户点击开始下载，下载完成开始覆盖安装程序，这样用户的应用就保持最新的。

<!--MORE-->

## 2.服务端

首先为了在任何地方都能比对版本，你必须要一个能提供最新版本信息的服务器，服务器的特定地址可以给出版本信息，一般都是web服务器。当然很多人不愿意为了更新版本就单独做一个网站，你可以不用自己的服务器，而把这些信息放到互联网的任何地方，只要能访问得到就行，甚至博客中，app根据一定的规则解析到需要的信息。因为已经有自己的网站，所以就直接放在网站上面，这样我可以在网站上集成发布app的功能。

这里我使用的是nginx，然后上面放了版本对比信息以及apk位置。**update.txt**配置信息如下：

`XXX&1.0.1&界面优化&http://192.168.43.143/logindemo.apk`

上面配置文件中&为分隔符，XXX为文件头，不用管随便写，1.0.1位版本信息看，界面优化部分为版本更新的描述，后面url为apk存放位置。

这里的update.txt放在服务器上的位置请记录好，比如我这里是http://192.168.43.143/update.txt，后面会用到。

## 3.Android端

在这里准备一个小例子用于实现软件自动更新的功能，主要实现了客户端通过访问服务端的配置文件update.txt，解析出版本信息、升级描述以及apk的url地址，然后进行下载到本地，并且跳转到安装页面，也就是实现了软件自动更新的操作。

另外值得注意的是，用以前实现安装apk的方法在Android 7.0中行不通了。根据Google对7.0有如下描述

**在 应 用 间 共 享 文 件** 

对 于 面 向 Android 7 . 0 的应 用 ， Android 框 架 执 行 的 St riCtM0de API 政 策 禁 止 在 您 的 应 用 外 部 公 开 file ： //URI, 如 果 一 顶 包 含 文 件 URI 的 intent 离 开 您 的 应 用 ， 则 应 用 出 现 故 障， 并 出 现 FiletJriExposedExcepti0n 异 常 。 要 在 应 用 间 共 享 文 件 ， 您 应发 送 一 项 content ： // URI, 并 授 予 URI 临 时 访 问 ， 又 限 。 进 行 此 授 权 的 最 简 单 方 式 是 使 用FileProvider 类 。 如 需 了 解 有 关 权 限 和 共 享 文 件 的 详 细 信 息 ，请 参阅 共 享 文 件 。 

在这里也将会一并解决。

1、 新建UpdateInfo.java，主要为升级apk的信息

~~~
public class UpdateInfo {
    private String version;
    private String description;
    private String url;    
    ---省略getter、setter方法
}
~~~

2、 获取服务端地址。新建GetServerUrl.java

```
public class GetServerUrl {
  static String url = "http://192.168.43.143/";
  public static String getUrl() {
  return url;
	}
}
```
3.新建一个UpdateInfoService.java，负责软件更新模块

	public class UpdateInfoService {
	  ProgressDialog progressDialog;
	  Handler handler;
	  Context context;
	  UpdateInfo updateInfo;
	
	  public UpdateInfoService(Context context) {
	      this.context = context;
	  }
	
	  public UpdateInfo getUpDateInfo() throws Exception {
	      String path = GetServerUrl.getUrl() + "/update.txt";
	      StringBuffer sb = new StringBuffer();
	      String line = null;
	      BufferedReader reader = null;
	      try {
	          URL url = new URL(path);
	          HttpURLConnection urlConnection = (HttpURLConnection) url
	                  .openConnection();
	          reader = new BufferedReader(new InputStreamReader(
	                  urlConnection.getInputStream()));
	          while ((line = reader.readLine()) != null) {
	              sb.append(line);
	          }
	      } catch (Exception e) {
	          e.printStackTrace();
	      } finally {
	          try {
	              if (reader != null) {
	                  reader.close();
	              }
	          } catch (Exception e) {
	              e.printStackTrace();
	          }
	      }
	      String info = sb.toString();
	      UpdateInfo updateInfo = new UpdateInfo();
	      updateInfo.setVersion(info.split("&")[1]);
	      updateInfo.setDescription(info.split("&")[2]);
	      updateInfo.setUrl(info.split("&")[3]);
	      this.updateInfo = updateInfo;
	      return updateInfo;
	  }
	  public boolean isNeedUpdate() {
	      String new_version = updateInfo.getVersion(); // ×îÐÂ°æ±¾µÄ°æ±¾ºÅ
	      String now_version = "";
	      try {
	          PackageManager packageManager = context.getPackageManager();
	          PackageInfo packageInfo = packageManager.getPackageInfo(
	                  context.getPackageName(), 0);
	          now_version = packageInfo.versionName;
	      } catch (NameNotFoundException e) {
	          e.printStackTrace();
	      }
	      if (new_version.equals(now_version)) {
	          return false;
	      } else {
	          return true;
	      }
	  }
	  public void downLoadFile(final String url, final ProgressDialog pDialog, Handler h) {
	      progressDialog = pDialog;
	      handler = h;
	      new Thread() {
	          public void run() {
	              HttpClient client = new DefaultHttpClient();
	              HttpGet get = new HttpGet(url);
	              HttpResponse response;
	              try {
	                  response = client.execute(get);
	                  HttpEntity entity = response.getEntity();
	                  int length = (int) entity.getContentLength();  
	                  progressDialog.setMax(length);                           
	                  InputStream is = entity.getContent();
	                  FileOutputStream fileOutputStream = null;
	                  if (is != null) {
	                      File file = new File(
	                              Environment.getExternalStorageDirectory(),
	                              "logindemo.apk");
	                      fileOutputStream = new FileOutputStream(file);
	                      byte[] buf = new byte[10];
	                      int ch = -1;
	                      int process = 0;
	                      while ((ch = is.read(buf)) != -1) {
	                          fileOutputStream.write(buf, 0, ch);
	                          process += ch;
	                          progressDialog.setProgress(process);
	                      }
	
	                  }
	                  fileOutputStream.flush();
	                  if (fileOutputStream != null) {
	                      fileOutputStream.close();
	                  }
	                  down();
	              } catch (ClientProtocolException e) {
	                  e.printStackTrace();
	              } catch (IOException e) {
	                  e.printStackTrace();
	              }
	          }
	
	      }.start();
	  }
	
	  void down() {
	      handler.post(new Runnable() {
	          public void run() {
	              progressDialog.cancel();
	              update();
	          }
	      });
	  }
	
	  void update() {
	
	      Intent intent = new Intent();
	      intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
	      intent.setAction(Intent.ACTION_VIEW);
	      //检查手机版本号，如果是Android7.0将采用应用共享方法
	      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {// android.os.FileUriExposedException
	          intent.setFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
	          Uri installURI = FileProvider.getUriForFile(context, "com.ali.huyd.fileprovider", new File(Environment
	                  .getExternalStorageDirectory(), "logindemo.apk"));
	          intent.setDataAndType(installURI,
	                  "application/vnd.android.package-archive");
	      } else {//其他版本直接调用
	          intent.setDataAndType(Uri.fromFile(new File(Environment
	                          .getExternalStorageDirectory(), "logindemo.apk")),
	                  "application/vnd.android.package-archive");
	      }
	      context.startActivity(intent);
	 	 }
	  }
4.在Activity中调用，点击更新按钮，会弹出对话框，显示版本号和升级描述。这里新建UpdateActivity.java

	public class UpdateActivity extends AppCompatActivity {

	  private UpdateInfo info;
	  private ProgressDialog progressDialog;
	  UpdateInfoService updateInfoService;
	
	  @Override
	  protected void onCreate(Bundle savedInstanceState) {
	      super.onCreate(savedInstanceState);
	      setContentView(R.layout.activity_update);
	      Button button = (Button) findViewById(R.id.button1);
	      button.setOnClickListener(new OnClickListener() {
	          @Override
	          public void onClick(View v) {
	              
	              checkUpdate();
	          }
	      });
	  }
	
	  private void checkUpdate() {
	      Toast.makeText(UpdateActivity.this, "checkUpdate..", Toast.LENGTH_SHORT).show();
	      
	      new Thread() {
	          public void run() {
	              try {
	                  updateInfoService = new UpdateInfoService(UpdateActivity.this);
	                  info = updateInfoService.getUpDateInfo();
	                  handler1.sendEmptyMessage(0);
	              } catch (Exception e) {
	                  e.printStackTrace();
	              }
	          }
	      }.start();
	  }
	
	  @SuppressLint("HandlerLeak")
	  private Handler handler1 = new Handler() {
	      public void handleMessage(Message msg) {
	          
	          if (updateInfoService.isNeedUpdate()) {
	              showUpdateDialog();
	          }
	      }
	
	      ;
	  };
	  //系统更新对话框
	  private void showUpdateDialog() {
	      AlertDialog.Builder builder = new AlertDialog.Builder(this);
	      builder.setIcon(android.R.drawable.ic_dialog_info);
	      builder.setTitle("更新版本" + info.getVersion());
	      builder.setMessage(info.getDescription());
	      builder.setCancelable(false);
	      builder.setPositiveButton("现在更新", new DialogInterface.OnClickListener() {
	          @Override
	          public void onClick(DialogInterface dialog, int which) {
	              if (Environment.getExternalStorageState().equals(
	                      Environment.MEDIA_MOUNTED)) {
	                  downFile(info.getUrl());
	              } else {
	                  Toast.makeText(UpdateActivity.this, "getExternalStorageState¨",
	                          Toast.LENGTH_SHORT).show();
	              }
	          }
	      });
	      builder.setNegativeButton("以后更新", new DialogInterface.OnClickListener() {
	          @Override
	          public void onClick(DialogInterface dialog, int which) {
	          }
	      });
	      builder.create().show();
	  }
		//显示下载进度
	  void downFile(final String url) {
	      progressDialog = new ProgressDialog(UpdateActivity.this);
	      progressDialog.setProgressStyle(ProgressDialog.STYLE_HORIZONTAL);
	      progressDialog.setTitle("下载新版本");
	      progressDialog.setMessage("下载中...");
	      progressDialog.setProgress(0);
	      progressDialog.show();
	      updateInfoService.downLoadFile(url, progressDialog, handler1);
	  }
	}
5.AndroidManifest.xml配置

由于Google API的限制，Android N这里我们需要使用FileProvider

    <provider
     		android:name="android.support.v4.content.FileProvider"
            android:authorities="com.ali.huyd.fileprovider"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/filepaths"/>
    </provider>
exported:要求必须为false，为true则会报安全异常。
grantUriPermissions:true，表示授予 URI 临时访问权限。
authorities 组件标识，按照江湖规矩,都以包名开头,避免和其它应用发生冲突。

另外加上相应权限

`<uses-permission android:name="android.permission.INTERNET"/>`

6.由于需要使用FileProvider，所以我们需要在res/xml/下新建file_paths

    <paths>
    <external-path
        name="my_demo"
        path=""/>
    </paths>
这里的path=""指的是根目录，即可以其他应用共享根目录和子目录下的所有资源



到这里就完成了。

![autoupdate_1](android-autoupdate\autoupdate_1.png)