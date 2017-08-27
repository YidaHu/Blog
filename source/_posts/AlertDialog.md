---
title: Android常用警示框实现
date: 2016-04-19 22:15:10
categories: Android
tags:
- AlertDialog
---

![420H](AlertDialog\420H.jpg)

# 1.前言

在做开发任务的时候，免不了会用到各种各样的对话框，今天在此记录一下简单的对话框实现方法，后面会逐渐补充，当做笔记记录一下，方便后面复用。

<!--MORE-->

## 普通警示框

	private void showAlert() {
			new AlertDialog.Builder(this)
					.setTitle("激活通知")
					.setMessage("此账户未激活,是否发送激活信息？")
					.setPositiveButton("是", new DialogInterface.OnClickListener() {
						@Override
						public void onClick(DialogInterface dialogInterface, int i) {
							new Thread(checkActivation).start();
						}
					})
					.setNegativeButton("否", null)
					.show();
		}

## 带输入框的警示框

	private void showActivation() {
			LayoutInflater factory = LayoutInflater.from(LoginActivity.this);//提示框
			final View view = factory.inflate(R.layout.alert_activation, null);//这里必须是final的
			final EditText edit = (EditText) view.findViewById(R.id.etCheckcode);//获得输入框对象
			new AlertDialog.Builder(this)
					.setTitle("请输入验证码")
					.setIcon(android.R.drawable.ic_dialog_info)
					.setView(view)
					.setPositiveButton("确定", new DialogInterface.OnClickListener() {
						@Override
						public void onClick(DialogInterface dialogInterface, int i) {
							String checkCode = edit.getText().toString();//获取输入框值
							new Thread(startActivation).start();	
						}
					})
					.setNegativeButton("取消", null)
					.show();
		}

## 自定义的警示框

警示框弹出，主要有从相册中选取和调用照相机等两个按钮，按钮为自定义布局（两个TextView），实现代码如下：

~~~
	private void showTypeDialog() {
		AlertDialog.Builder builder = new AlertDialog.Builder(this);
		final AlertDialog dialog = builder.create();
		View view = View.inflate(this, R.layout.dialog_select_img, null);
		TextView tv_select_gallery = (TextView) view.findViewById(R.id.tv_select_gallery);
		TextView tv_select_camera = (TextView) view.findViewById(R.id.tv_select_camera);
		tv_select_gallery.setOnClickListener(new View.OnClickListener() {// 在相册中选取
			@Override
			public void onClick(View v) {
				selectFromAlbum();
				dialog.dismiss();
			}
		});
		tv_select_camera.setOnClickListener(new View.OnClickListener() {// 调用照相机
			@Override
			public void onClick(View v) {
				openCamera(UserinfoActivity.this);
				dialog.dismiss();
			}
		});
		dialog.setView(view);
		dialog.show();
	}
~~~

