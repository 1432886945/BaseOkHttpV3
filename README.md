# BaseOkHttp V3

<a href="https://github.com/kongzue/BaseOkHttp/">
<img src="https://img.shields.io/badge/BaseOkHttp-3.0.0-green.svg" alt="BaseOkHttp">
</a>
<a href="https://bintray.com/myzchh/maven/BaseOkHttp/3.0.0/link">
<img src="https://img.shields.io/badge/Maven-3.0.0-blue.svg" alt="Maven">
</a>
<a href="http://www.apache.org/licenses/LICENSE-2.0">
<img src="https://img.shields.io/badge/License-Apache%202.0-red.svg" alt="License">
</a>
<a href="http://www.kongzue.com">
<img src="https://img.shields.io/badge/Homepage-Kongzue.com-brightgreen.svg" alt="Homepage">
</a>

## 简介
- BaseOkHttp V3是基于BaseOkHttp V2( https://github.com/kongzue/BaseOkHttp )的升级版本，基于能够快速创建常用请求链接而封装的库。
- 本库中自带 OkHttp 库，并对其关联的 okio 库进行了包名的修改和封装，因此不会影响到您项目中的其他版本的 okHttp 库，亦不会产生冲突。
- 若请求来自于一个 Activity，结束请求后自动回归主线程操作，不需要再做额外处理。
- 提供统一返回监听器ResponseListener处理返回数据，避免代码反复臃肿。
- 强大的全局方法和事件让您的请求得心应手。

## Maven仓库或Gradle的引用方式
Maven仓库：
```
<dependency>
  <groupId>com.kongzue.baseokhttp_v3</groupId>
  <artifactId>baseokhttp_v3</artifactId>
  <version>3.0.0</version>
  <type>pom</type>
</dependency>
```
Gradle：

在dependencies{}中添加引用：
```
implementation 'com.kongzue.baseokhttp_v3:baseokhttp_v3:3.0.0'
```

试用版可以前往 http://fir.im/BaseOkHttp 下载

## 一般请求
BaseOkHttp V3 提供两种请求写法，范例如下：

以参数形式创建请求：
```
progressDialog = ProgressDialog.show(context, "请稍候", "请求中...");
HttpRequest.POST(context, "http://你的接口地址", new Parameter().add("page", "1"), new ResponseListener() {
    @Override
    public void onResponse(String response, Exception error) {
        progressDialog.dismiss();
        if (error == null) {
            resultHttp.setText(response);
        } else {
            resultHttp.setText("请求失败");
            Toast.makeText(context, "请求失败", Toast.LENGTH_SHORT).show();
        }
    }
});
```
一般请求中，使用 HttpRequest.POST(...) 方法可直接创建 POST 请求，相应的，HttpRequest.GET(...) 可创建 GET 请求，另外可选额外的方法增加 header 请求头：
```
HttpRequest.POST(Context context, String url, Parameter headers, Parameter parameter, ResponseListener listener);
HttpRequest.GET(Context context, String url, Parameter headers, Parameter parameter, ResponseListener listener);
```

或者也可以以流式代码创建请求：
```
progressDialog = ProgressDialog.show(context, "请稍候", "请求中...");
HttpRequest.build(context,"http://你的接口地址")
        .addHeaders("Charset", "UTF-8")
        .addParameter("page", "1")
        .addParameter("token", "A128")
        .setResponseListener(new ResponseListener() {
            @Override
            public void onResponse(String response, Exception error) {
                progressDialog.dismiss();
                if (error == null) {
                    resultHttp.setText(response);
                } else {
                    resultHttp.setText("请求失败");
                    Toast.makeText(context, "请求失败", Toast.LENGTH_SHORT).show();
                }
            }
        })
        .doPost();
```
返回回调监听器只有一个，请在其中对 error 参数判空，若 error 不为空，则为请求失败，反之则为请求成功，请求成功后的数据存放在 response 参数中。

之所以将请求成功与失败放在一个回调中主要目的是方便无论请求成功或失败都需要执行的代码，例如上述代码中的 progressDialog 等待对话框都需要关闭（dismiss掉），这样的写法更为方便。

## Json请求
有时候我们需要使用已经处理好的json文本作为请求参数，此时可以使用 HttpRequest.JSONPOST(...) 方法创建 json 请求。

json 请求中，参数为文本类型，创建请求方式如下：
```
progressDialog = ProgressDialog.show(context, "请稍候", "请求中...");
HttpRequest.JSONPOST(context, "http://你的接口地址", "{\"key\":\"DFG1H56EH5JN3DFA\",\"token\":\"124ASFD53SDF65aSF47fgT211\"}", new ResponseListener() {
    @Override
    public void onResponse(String response, Exception error) {
        progressDialog.dismiss();
        if (error == null) {
            resultHttp.setText(response);
        } else {
            resultHttp.setText("请求失败");
            Toast.makeText(context, "请求失败", Toast.LENGTH_SHORT).show();
        }
    }
});
```
Json请求中，可使用 HttpRequest.JSONPOST(...) 快速创建 Json 请求，另外可选额外的方法增加 header 请求头：
```
HttpRequest.JSONPOST(Context context, String url, Parameter headers, String jsonParameter, ResponseListener listener)
```

也可以使用流式代码创建请求：
```
progressDialog = ProgressDialog.show(context, "请稍候", "请求中...");
HttpRequest.build(context,"http://你的接口地址")
        .setJsonParameter("{\"key\":\"DFG1H56EH5JN3DFA\",\"token\":\"124ASFD53SDF65aSF47fgT211\"}")
        .setResponseListener(new ResponseListener() {
            @Override
            public void onResponse(String response, Exception error) {
                progressDialog.dismiss();
                if (error == null) {
                    resultHttp.setText(response);
                } else {
                    resultHttp.setText("请求失败");
                    Toast.makeText(context, "请求失败", Toast.LENGTH_SHORT).show();
                }
            }
        })
        .doPost();
```
Json请求只能以 POST 的方式进行，如果执行doGet(); 方法也会以 POST 的方式发出。

## 文件上传
要使用文件上传就需要将 File 类型的文件作为参数传入 Parameter，此时参数中亦可以传入其他文本类型的参数。

一旦参数传入文件，请求必然为 POST 类型，即便调用了 HttpRequest.GET(...) 也会当作 POST 类型的请求发出。

范例代码如下：
```
progressDialog = ProgressDialog.show(context, "请稍候", "请求中...");
HttpRequest.POST(context, "http://你的接口地址", new Parameter()
                         .add("key", "DFG1H56EH5JN3DFA")
                         .add("imageFile1", file1)
                         .add("imageFile2", file2)
        , new ResponseListener() {
            @Override
            public void onResponse(String response, Exception error) {
                progressDialog.dismiss();
                if (error == null) {
                    resultHttp.setText(response);
                } else {
                    resultHttp.setText("请求失败");
                    Toast.makeText(context, "请求失败", Toast.LENGTH_SHORT).show();
                }
            }
        });
```

也可以使用流式代码创建请求：
```
HttpRequest.build(context,"http://你的接口地址")
        .addHeaders("Charset", "UTF-8")
        .addParameter("page", "1")
        .addParameter("imageFile1", file1)
        .addParameter("imageFile2", file2)
        .setResponseListener(new ResponseListener() {
            @Override
            public void onResponse(String response, Exception error) {
                progressDialog.dismiss();
                if (error == null) {
                    resultHttp.setText(response);
                } else {
                    resultHttp.setText("请求失败");
                    Toast.makeText(context, "请求失败", Toast.LENGTH_SHORT).show();
                }
            }
        })
        .doPost();
```

默认上传文件使用的 mediaType 为 "image/png"，可使用以下代码进行修改：
```
.setMediaType(MediaType.parse("application/pdf"))       //设置为pdf类型
```

类型参考如下：

内容 | 含义
---|---
text/html | HTML格式
text/plain | 纯文本格式
text/xml |  XML格式
image/gif | gif图片格式
image/jpeg | jpg图片格式
image/png | png图片格式
application/xhtml+xml | XHTML格式
application/xml     |  XML数据格式
application/atom+xml  | Atom XML聚合格式
application/json    |  JSON数据格式
application/pdf       | pdf格式
application/msword  |  Word文档格式
application/octet-stream | 二进制流数据（如常见的文件下载）
multipart/form-data | 表单数据

## 额外功能

### 全局日志
全局日志开关（默认是关闭态，需要手动开启）：
```
BaseOkHttp.DEBUGMODE = true;
```
BaseOkHttp V3支持增强型日志，使用输出日志内容是 json 字符串时，会自动格式化输出，方便查看。

![BaseOkHttp Logs](https://github.com/kongzue/Res/raw/master/app/src/main/res/mipmap-xxxhdpi/img_okhttp_logs.png)

在您使用 BaseOkHttp 时可以在 Logcat 的筛选中使用字符 “>>>” 对日志进行筛选（Logcat日志界面上方右侧的搜索输入框）。

您可以在 Android Studio 的 File -> Settings 的 Editor -> Color Scheme -> Android Logcat 中调整各类型的 log 颜色，我们推荐如下图方式设置颜色：

![Kongzue's log settings](https://github.com/kongzue/Res/raw/master/app/src/main/res/mipmap-xxxhdpi/baseframework_logsettings.png)

### 全局请求地址
设置全局请求地址后，所有接口都可以直接使用相对地址进行，例如设置全局请求地址：
```
BaseOkHttp.serviceUrl = "https://www.example.com";
```
发出一个请求：
```
HttpRequest.POST(context, "/femaleNameApi", new Parameter().add("page", "1"), new ResponseListener() {...});
```
那么实际请求地址即 https://www.example.com/femaleNameApi ，使用更加轻松方便。

### 全局 Header 请求头
使用如下代码设置全局 Header 请求头：
```
BaseOkHttp.overallHeader = new Parameter()
        .add("Charset", "UTF-8")
        .add("Content-Type", "application/json")
        .add("Accept-Encoding", "gzip,deflate")
;
```

### 全局请求返回拦截器
使用如下代码可以设置全局返回数据监听拦截器，return true 可返回请求继续处理，return false 即拦截掉不会继续返回原请求进行处理；
```
BaseOkHttp.responseInterceptListener = new ResponseInterceptListener() {
    @Override
    public boolean onResponse(Context context, String url, String response, Exception error) {
        if (error != null) {
            return true;
        } else {
            Log.i("!!!", "onResponse: " + response);
            return true;
        }
    }
};
```

### HTTPS 支持
1) 请将SSL证书文件放在assets目录中，例如“ssl.crt”；
2) 以附带SSL证书名的方式创建请求：
```
BaseOkHttp.SSLInAssetsFileName = "ssl.crt";
...
```
即可使用Https请求方式。

另外，可使用 BaseOkHttp.httpsVerifyServiceUrl=(boolean) 设置是否校验请求主机地址与设置的 HttpRequest.serviceUrl 一致；

### 全局参数拦截器
使用如下代码可以设置全局参数监听拦截器，此参数拦截器可以拦截并修改、新增所有请求携带的参数。

此方法亦适用于需要对参数进行加密的场景：
```
BaseOkHttp.parameterInterceptListener = new ParameterInterceptListener() {
    @Override
    public Parameter onIntercept(Parameter parameter) {
        parameter.add("key", "DFG1H56EH5JN3DFA");
        parameter.add("sign", makeSign(parameter.toParameterString()));
        return parameter;
    }
};

private String makeSign(String parameterString){
    //加密逻辑
    ...
}
```

### 请求超时
使用以下代码设置请求超时时间（单位：秒）
```
BaseOkHttp.TIME_OUT_DURATION = 10;
```

## 开源协议
```
Copyright Kongzue BaseOkHttp

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

 http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

本项目中使用的网络请求底层框架为square.okHttp3(https://github.com/square/okhttp )，感谢其为开源做出的贡献。

相关协议如下：
```
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```