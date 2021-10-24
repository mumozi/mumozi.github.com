## js ajax 关于post请求接口导出文件的写法

现在不管后端使用什么插件，都会生成一个文件流，然后大部分情况下会使用window.location的方式请求打开这个url，然后直接下载文件

但是有时候在兼顾查询使用了post方式的情况下，使用post方式请求导出可以减轻部分工作量，于是开始探索相关写法，最终代码如下：

```js
$.ajax({
    url: url,
    type: 'POST',
    data: {
        param: Jparam
    },
    xhrFields: { responseType: "blob" },
    success: function(data,status,request) {
        var disp = request.getResponseHeader('Content-Disposition')
        layer.close(dh);
        var fileName
        let filenameRegex = /filename[^;=\n]*=((['"]).*?\2|[^;\n]*)/
        let matches = filenameRegex.exec(disp)
        if (matches != null && matches[1]) {
            fileName = matches[1].replace(/['"]/g, '')
        }
        // 通过 URLEncoder.encode(pFileName, StandardCharsets.UTF_8.name()) 加密编码的, 使用decodeURI(fileName) 解密
        fileName = decodeURI(fileName)
        var urlCreator = window.URL || window.webkitURL;
        var blob = new Blob([data], { type: "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet" });
        var url = urlCreator.createObjectURL(blob); //这个函数的返回值是一个字符串，指向一块内存的地址。
        //以下代码保存我的excel导出文件
        var link = document.createElement('a'); //创建事件对象
        link.setAttribute('href', url);
        link.setAttribute("download", fileName);
        var event = document.createEvent("MouseEvents"); //初始化事件对象
        event.initMouseEvent("click", true, true, document.defaultView, 0, 0, 0, 0, 0, false, false, false, false, 0, null); //触发事件
        link.dispatchEvent(event);
    }
})

```

由于文件名是从`ResponseHeader('Content-Disposition')`中读取的，读取时发现了一个问题，即中文会出现乱码，这种情况需要后端将文件名中文部分进行`urlencode`处理，然后前端通过`decodeURI()`解析即可
如果没有需求，可以写死filename，无需解析`ResponseHeader('Content-Disposition')`