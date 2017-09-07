前段时间项目中有个需求，将数据导出为csv。最近闲下来，整理下相关知识点。

##  What is Blob

> 一个 Blob对象表示一个不可变的, 原始数据的类似文件对象。Blob表示的数据不一定是一个JavaScript原生格式。 File 接口基于Blob，继承 blob功能并将其扩展为支持用户系统上的文件。

通俗来说, Blob 是一个js对象类型，存着二进制数据。

## How to use
直接通过构造函数进行创建。

### 构造函数:
```JavaScript
Blob(blobParts[, options])
```
 options是一个对象，可以包含下面两个属性：
- type -- [MIME类型](http://www.w3school.com.cn/media/media_mimeref.asp), 类型未知返回空字符串
- endings -- 决定 append() 的数据格式(数据中的 \n 如何被转换)，可以取值为"transparent"(不变, 默认)或者"native"(按操作系统转换);
```JavaScript
let debug = {debug: 'this is a test'};
let blob = new Blob(JSON.stringify(debug, null, 2));
```

### 方法 slice
```JavaScript
var blob = instanceOfBlob.slice([start [, end [, contentType]]]};
```
返回一个新的 Blob 对象，包含了源 Blob 对象中指定范围内的数据。

## 实例
### 使用 Blob 下载文件。
```JavaScript
/**
 * 使用 Blob 下载文本，字符串，json
 *
 * @param {String} content 已转string的文本、json等
 * @param {String} filename 下载时文件取名为？
 **/
const funcDownload = (content, filename, type = 'text/plain') => {
    let downLink = document.createElement('a');

    // 支持a链接下载？
    if (!('download' in downLink)) return false;

    downLink.download = filename;
    downLink.style.display = 'none';

    // 字符串内容转blob地址
    let blobURL = new Blob([content], {type});
    downLink.href = URL.createObjectURL(blobURL);

    // 触发下载
    document.body.appendChild(downLink);
    downLink.click();

    // 移除 a 节点
    document.body.removeChild(downLink);
};
```

### 通过二进制传输图片的浏览器端处理
假设需求是这样的，二进制数据的第一位是一个标识符，指代该图的分类，之后是image的二进制数据。
```
ws.onmessage = (event) => {
    if(event.data.instanceof Blob) {
        let blob = event.data;

        // 拆分 blob
        const blobImgFlag = blob.slice(0, 1);
        const blobImg = blob.slice(1);

        // 将 blob 的 img flag 转成字符串
        let reader = new FileReader();
        reader.readAsBinaryString(blobImgFlag);

        // 读取成功 callback
        reader.onload = function (evt) {
            if(evt.target.readyState === FileReader.DONE) {
                const imgFlag = evt.target.result;

                // ... 根据得到的 imgFlag 做点啥， 如
                let img = document.getElementById(`img-${imgFlag}`);

                // 显示在页面中
                img.src = URL.createObjectURL(blogImg);
            }
        }
    }
}
```

### 将表格下载到 csv
这里其实用的就是第一个实例的方法，不过如果涉及到中文，需要指定下编码跟加上BOM头防乱码。
```
// 你需要把数据拼接为字符串，单元格与单元格用逗号(,)分隔， 行与行用换行符(\r\n)分隔。 如：
let content = '时间,正常(0<=评分<70),疑似(70<=评分<90),欺诈(90<=评分<=100),\r\n2017/09/07 00:00,0,0,0,\r\n2017/09/07 01:00,0,0,0,\r\n2017/09/07 02:00,0,0,0,\r\n2017/09/07 03:00,0,0,0,\r\n2017/09/07 04:00,0,0,0,\r\n2017/09/07 05:00,0,0,0,\r\n2017/09/07 06:00,0,0,0,\r\n2017/09/07 07:00,0,0,0,\r\n2017/09/07 08:00,0,0,0,\r\n2017/09/07 09:00,0,0,0,\r\n';
const fileName = 'balabala.csv';

// 如果有问题，试试 'text/csv;chartset=utf-8'
const type = 'text/plain;chartset=utf-8';

funcDownload(content, fileName, type);
```

## Reference
- [MDN/API/Blob](https://developer.mozilla.org/zh-CN/docs/Web/API/Blob)
- [js中从blob提取二进制](http://www.cnblogs.com/redlight/p/3905326.html)