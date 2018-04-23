# EasyJSBridge

EasyJSBridge让JS在Android/iOS WebView中反调接口统一，调用更容易；

## JS调用Demo

```JavaScript

<script type="text/javascript" src="EasyJSBridge.js"></script>
<script type="text/javascript">
    var methods = ["test1", "test2", "test3"];
    var easyJSBridge = EasyJSBridge.create("android", "ios", methods);

    $(".test1").click(function() {
        easyJSBridge.test1("parameter1")
    });

    $(".test2").click(function() {
        easyJSBridge.test2("parameter1", 2)
    });
    $(".test3").click(function() {
        easyJSBridge.test3("androidParameter1", 2, ["iosParameter1", 2, "3"])
    });
</script>


```
## 约定

- 在Android中通过`webView.addJavascriptInterface(obj,'android')` 绑定反调；
- 在iOS中通过`WKWebView`的`WKScriptMessageHandler`绑定反调，可以有两种方式绑定，二选一即可： 
  
  1. 通过addScriptMessageHandler添加一个name，然后通过约定的参数来解析要调用的方法和参数

        ```
        [userContentController addScriptMessageHandler:self name:@"iOS"]; 

        ```

        然后解析js调用的数据：

        ```
        window.webkit.messageHandlers.iOS.postMessage({method:'test1',parameter:['','']});
        window.webkit.messageHandlers.iOS.postMessage({method:'test2',parameter:['','']})
        ```
       注意`{method:'test1',parameter:['','']}`中的`method`和`parameter`必须约定如此,`parameter`是参数数组，iOS需自己解析参数；

  2. 通过addScriptMessageHandler添加多个name，然后根据不同name来区分调用的方法

       ```
       [userContentController addScriptMessageHandler:self name:@"test1"];
       [userContentController addScriptMessageHandler:self name:@"test2"]; 

       ```

       ```
   
       if ([message.name isEqualToString:@"test1"]) {
  
    
       } else if ([message.name isEqualToString:@"test2"]) {
     
       }
       ```
   
       然后解析js调用的数据：

       ```
       window.webkit.messageHandlers.test1.postMessage('arg1',2)
       window.webkit.messageHandlers.test2.postMessage('arg1','arg2')
       ```
- JS调用中，约定的反调方法名称都要显式在数组中初始化

  ```
    var methods = ["test1", "test2", "test3"];
    var easyJSBridge = EasyJSBridge.create("android", "ios", methods);
  ```

## API

### 初始化

var easyJSBridge = EasyJSBridge.create('androidObj','iOSObj',[methodList])

|参数名|必选|类型|说明|
|:----    |:---|:----- |-----   |
|androidObj |是  |string |Android WebView绑定的对象名称  |
|iOSObj |否  |string | iOS WebView绑定的对象名称    |
|methodList     |是  |数组 | 方法名称列表    |

**备注** 

- 如果有iOSObj，那就按照如下方法约定反调
  
 ```
    window.webkit.messageHandlers.iOS.postMessage({method:'test1',parameter:['','']});
    window.webkit.messageHandlers.iOS.postMessage({method:'test2',parameter:['','']})
 ```

- 如果没有iOSObj，那就按照如下方法约定反调

 ```
    window.webkit.messageHandlers.test1.postMessage('arg1',2)
    window.webkit.messageHandlers.test2.postMessage('arg1','arg2')
 ```

### 调用反调

先初始化

```
var methodList = ['method1','method2'];
var easyJSBridge = EasyJSBridge.create('androidObj','iOSObj',methodList);

```

然后根据初始化时的methodList方法名直接调用

```
easyJSBridge.method1("arg1","arg2");
easyJSBridge.method2("arg1","arg2");
```

**备注** 

如果Android和iOS调用参数值不一样，或者参数不一样，可以如下调用：

```
easyJSBridge.method1("androidArg1","androidArg2",["iOSArg1","iOSArg2","iOSArg3"]);
```
method1中的数组参数给iOS用，前面的参数给android用


## License

```
MIT License

Copyright (c) 2018 Yale

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```