---
title: nodejs stdout重定向到文件(日志写入)
comments: true
date: 2016-4-12 20:13:54
categories: [nodejs]
tags: [nodejs]
keywords: nodejs stdout重定向
---

### nodejs 调试日志写入自定义文件

1. 最简单的方式重定向标准输出(stdout)  
    - 启动的时候重定向  
        ```
         node app.js > d:/tmp/app.log
        ```
    - 如上启动后, console.log 输出的日志将会写入到文件中, console.error 仍在控制台输出


2. 修改 `console` 对象

    ```javascript
    // 写入文件
    function writeFile(file, content){ 
        var fs = require('fs'); 
          
        // appendFile，如果文件不存在，会自动创建新文件  
        // 如果用writeFile，那么会删除旧文件，直接写新文件  
        fs.appendFile(file, "\r\n" + content, function(err){  
            if(err)  
                console.info("fail " + err);  
            else  
                console.info("写入文件ok");  
        });  
    }  

    console.log = function(msg){
      process.stdout.write('Log data: ' + msg);
      writeFile('d:/tmp/test.log', msg);
    };

    console.log('test about 中文');
    console.error('test2 error'); // 正常输出,没有重新定义此方法
    ```

3. 通过重置process.stdout 对象, log 和 error 全部写入test.log 

    ```javascript
    var fs = require('fs');
    var logStream = fs.createWriteStream('d:/tmp/test.log', {flags: 'a'});
    
    // 测试文件写入
    //logStream.write("My first row\n");
    //logStream.write("My second row\n");

    process.stdout.write = process.stderr.write = logStream.write.bind(logStream);
    console.log('test1');
    console.error('test2 error');
    ```

4. 开启子进程时，运行命令行重定向
    ```javascript
    var fs = require('fs');
    var logStream = fs.createWriteStream('d:/tmp/test.log', {flags: 'a'});

    logStream.write("My first row\n");
    logStream.write("My second row\n");

    var spawn = require('child_process').spawn,
        ls    = spawn('ls', ['-al', 'd:/']);

    ls.stdout.pipe(logStream);  // 结果写入文件
    ls.stderr.pipe(logStream);

    ls.stdout.on('data', function (data) {
      console.log('stdout: ' + data);  // 输出到控制台
    });

    ls.on('close', function (code) {
      console.log('child process exited with code ' + code); // 输出到控制台
    });

    ```


### 有时间抽空看 pm2, forerver 模块日志写入的方式

