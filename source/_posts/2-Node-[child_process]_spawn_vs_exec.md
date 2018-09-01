---
title: Node.js child_processs中的pawn和exec 
comments: true
date: 2016-4-12 20:13:39
categories: [nodejs]
tags: [nodejs, 转载]
keywords: nodejs child_process pawn和exec 
---


#### spawn和exec 的使用

Node.js的child_process模块中有两个方法spawn和exec，这两个方法都可以被用来开启一个子进程来执行其他的程序。一些Node.js的新手常常对这个两个方法感到很困惑：既然两个方法的功能一样，那么究竟应该选择哪个方法。在本文中，我们将一起来探索spawn和我exec方法的不同之处，以便你在将来能够选择正确的方法。

child_process.spaen会返回一个带有stdout和stderr流的对象。你可以通过stdout流来读取子进程返回给Node.js的数据。stdout拥有’data’,’end’以及一般流所具有的事件。当你想要子进程返回大量数据给Node时，比如说图像处理，读取二进制数据等等，你最好使用spawn方法。

child_process.spawn方法是“异步中的异步”，意思是在子进程开始执行时，它就开始从一个流总将数据从子进程返回给Node。

下面是一个例子，比如说我们想从一个URL下载文件，我们选择使用curl工具，此时，我们就可以在Node中使用spawn运行curl工具，下面是具体代码：

```javascript
  // 使用curl下载文件的函数
  var download_file_curl = function(file_url) {

      // 提取文件名
      var file_name = url.parse(file_url).pathname.split('/').pop();

      // 创建一个可写流的实例
      var file = fs.createWriteStream(DOWNLOAD_DIR + file_name);
      // 使用spawn运行curl
      var curl = spawn('curl', [file_url]);

      // 为spawn实例添加了一个data事件
      curl.stdout.on('data', function(data) { file.write(data); });

      // 添加一个end监听器来关闭文件流
      curl.stdout.on('end', function(data) {
        file.end();
        console.log(file_name + ' downloaded to ' + DOWNLOAD_DIR);
      });

      // 当子进程退出时，检查是否有错误，同时关闭文件流
      curl.on('exit', function(code) {
        if (code != 0) {
          console.log('Failed: ' + code);
        }
      });
  };

```


child_process.exec方法会从子进程中返回一个完整的buffer。默认情况下，这个buffer的大小应该是200k。如果子进程返回的数据大小超过了200k，程序将会崩溃，同时显示错误信息“Error：maxBuffer exceeded”。你可以通过在exec的可选项中设置一个更大的buffer体积来解决这个问题，但是你不应该这样做，因为exec本来就不是用来返回很多数据的方法。对于有很多数据返回的情况，你应该使用上面的spawn方法。那么exec究竟是用来做什么的呢？我们可以使用它来运行程序然后返回结果的状态，而不是结果的数据。

child_process.exec方法是“同步中的异步”，意思是尽管exec是异步的，它一定要等到子进程运行结束以后然后一次性返回所有的buffer数据。如果exec的buffer体积设置的不够大，它将会以一个“maxBuffer exceeded”错误失败告终。

和上面一样，我们现在还是想要从一个URL下载文件。不同的是，我们现在要使用wget方法而不是curl方法，此时我们就需要使用exec方法在Node中执行wget命令，同时在子进程运行完毕后返回结果信息。下面是具体代码：

```javascript
    // 使用wget下载文件的函数
    var download_file_wget = function(file_url) {

        // 提取文件名
        var file_name = url.parse(file_url).pathname.split('/').pop();
        // 组合wget命令
        var wget = 'wget -P ' + DOWNLOAD_DIR + ' ' + file_url;
        // 使用exec执行wget命令
  
        var child = exec(wget, function(err, stdout, stderr) {
          if (err) throw err;
          else console.log(file_name + ' downloaded to ' + DOWNLOAD_DIR);
        });
    };
```

现在，你应该已经很清楚spawn和exec之间的区别了。总结一下：当你想要从子进程返回大量数据时使用spawn，如果只是返回简单的状态信息，那么使用exec。



> 原文地址: [说说Node.js child_process模块中的spawn和exec方法 ](http://www.html-js.com/article/A-day-to-learn-to-talk-about-JavaScript-spawn-and-exec-Nodejs-in-the-childprocess-module?utm_source=tuicool&utm_medium=referral)