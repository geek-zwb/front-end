# 传统 AJAX 
XMLHttpRequest() 是一个用于构建 HTTP 请求的 JavaScript 对象 (构造器);

AJAX: Asynchronous JavaScript and XML

## 发送请求
```
var xhr = new XMLHttpRequest();

// 监听器
xhr.onreadystatechange = myCallback;

// param1: HTTP请求类型 GET POST HEAD
// param2: 请求的 url
// param3: true 为异步， false为同步(阻塞 JavaScript 执行，等待请求结果返回)
xhr.open('GET', 'url', true);

xhr.send('');
```

## 处理响应
```JavaScript
// 注意同时满足条件
function myCallback() {
   if(xhr.readyState < 4) {
       return;
   } 
   
   if(xhr.status !== 200) {
       alert('Error!');
       return;
   }
   
   // do something
   alert(xhr.responseText);
}
```

## 封装
```
// GET
function request(url, callback) {
    var xhr = new XMLHttpRequest();
    xhr.onreadystatechange = (function(myxhr) {
        if (myxhr.readyState === 4 && myxhr.status === 200) {
            callback(myxhr);
        }
    })(xhr);
    
    xhr.open('GET', url, true);
    
    xhr.send('');
}
```

## 浏览器兼容
IE >= 7
```
var xhr;

if(window.XMLHttpRequest) {
    xhr = new XMLHttpRequest();
} else {
    xhr = new ActiveXObject("Microsoft.XMLHTTP");
}
```
还有一个特殊情况是，如果要在IE8和9上进行跨域实现，使用 XDomainRequest;
```
if(window.XDomainRequest){
  var xdr = new XDomainRequest();

  xdr.open("get", "http://example.com/api/method");

  xdr.onprogress = function () {
    //Progress
  };

  xdr.ontimeout = function () { 
    //Timeout
  };

  xdr.onerror = function () { 
    //Error Occured
  };

  xdr.onload = function() {
    //success(xdr.responseText);
  }

  setTimeout(function () {
    xdr.send();
  }, 0);
}
```
> [XDomainRequest 详情链接](https://developer.mozilla.org/zh-CN/docs/Web/API/XDomainRequest)


# Fetch API
> The Fetch API provides a JavaScript interface for accessing and manipulating parts of the HTTP pipeline, such as requests and responses. It also provides a global fetch() method that provides an easy, logical way to fetch resources asynchronously across the network.

an example
```
// get
fetch('flowers.jpg').then(function(response) {
  if(response.ok) {
    response.blob().then(function(myBlob) {
      var objectURL = URL.createObjectURL(myBlob);
      myImage.src = objectURL;
    });
  } else {
    console.log('Network response was not ok.');
  }
})
.catch(function(error) {
  console.log('There has been a problem with your fetch operation: ' + error.message);
});

// post
fetch("http://www.example.org/submit.php", {
  method: "POST",
  headers: {
    "Content-Type": "application/x-www-form-urlencoded"
  },
  body: "firstName=Nikhil&favColor=blue&password=easytoguess"
}).then(function(res) {
  if (res.ok) {
    alert("Perfect! Your settings are saved.");
  } else if (res.status == 401) {
    alert("Oops! You are not authorized.");
  }
}, function(e) {
  alert("Error submitting form!");
});
```

由于有 fetch-pollyfill, 所以尽量使用 fetch 进行 http 请求。
推荐 axios 库。

[使用 Fetch -- MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch)