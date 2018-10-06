---
title: Angular post请求SpringMVC后台接收不到参数解决方案
date: 2018-01-21 23:58:23
categories: WEB
tags:
- Angular
- POST请求
---

![216H](AngularPost请求参数\216H.jpg)

# 1.前言

Angular中的post请求传递参数默认使用的请求header是’Content-Type’: ‘application/json’,所以后端MVC框架的请求header如果不是这个，那么是无法解析的。下面提供Angular post请求SpringMVC后台接收不到参数的解决方案。今天遇到的问题，在此记录一下，以防忘记。

<!--MORE-->

angularjs的post请求的"Content-Type"默认为" application/josn",而 jquery的post请求的"Content-Type"默认为" application/x-www-form-urlencoded"。所以，要想SpringMVC后台接收到参数。需要修改Angular的post请求的Content-Type为x-www-form-urlencodedand。当然，此时后台仍然接受不到数据。

默认情况下，jQuery传输数据使用Content-Type: x-www-form-urlencodedand和类似于"foo=bar&baz=moe"的序列，然而Angular，传输数据使用Content-Type: application/json和{ "username": "admin", "password": "hello" }这样的json序列。所以把Content-Type设置成x-www-form-urlencodedand。

之后，还需要转换序列的格式。

#### TIP 1 : component.ts，传递参数到服务

```typescript
_submitForm() {
    if (this.validateForm.valid) {
      // 下面5行是关键
      let urlSearchParams = new URLSearchParams();
      urlSearchParams.append('username', this.username);
      urlSearchParams.append('password', this.password);
      urlSearchParams.append('roleId', this.roleId.value);
      let param = urlSearchParams.toString()
      //上面5行是关键
      this.loginServeService.login(param, data => {
        if (data.message == "SUCCESS") {
          this.router.navigate(['main']);
        } else {
          this.errorInfo = true;
        }
      });
    }
  }
```

#### TIP 2 :service.ts

```typescript
login(params,callback) {
  //post请求
    this.http.post('http://localhost:8080/api/login', params, {
      //请求头，关键！
      headers: new HttpHeaders().set('Content-Type', 'application/x-www-form-urlencoded;charset=utf-8'),
    }).subscribe(data => {
      callback(data);
    })
  }
```

