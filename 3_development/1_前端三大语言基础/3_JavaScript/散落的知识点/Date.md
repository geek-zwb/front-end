## Date() 构造函数
参数:
- 无参数（默认当天的日期）
- 一个用于表现日期的字符串
- 分开传递的日、月、时间等值
- 一个 timestamp 值

更精确的是向 Date() 构造器传递一些具体的数值：
- 年份
- 月份 从 0 （1月）11（12月）
- 日期 从 1 到 31
- 时数 从 1 到23
- 分钟 从 0 到 59
- 秒钟 从 0 到 59
- 毫秒数 从 0 到 999

需要注意的是，如果所传递的值超过了被允许的范围， Date 对象会自行启动 溢出式 前进处理。
如
```
new Date(2012, 11, 32); // Tue Jan 01 2013 00:00:00 GMT-0800 (PST)
```

## Date 对象方法
### 实例方法
```
// 详细找下边
get*(), set*()
```
### "静态方法"
```
Date.parse('Jan 11, 2018'); // 1515657600000

Date.UTC(2018, 0, 11); // 1515628800000 得到的是格林尼治时间

Date.now() === new Date().getTime(); // true
```

## Date 的扩展 format
### 1
```
    // 对Date的扩展，将 Date 转化为指定格式的String   
    // 月(M)、日(d)、小时(h)、分(m)、秒(s)、季度(q) 可以用 1-2 个占位符，   
    // 年(y)可以用 1-4 个占位符，毫秒(S)只能用 1 个占位符(是 1-3 位的数字)   
    // 例子：   
    // (new Date()).Format("yyyy-MM-dd hh:mm:ss.S") ==> 2006-07-02 08:09:04.423   
    // (new Date()).Format("yyyy-M-d h:m:s.S")      ==> 2006-7-2 8:9:4.18   
    Date.prototype.Format = function(fmt)   
    { //author: meizz   
      var o = {   
        "M+" : this.getMonth()+1,                 //月份   
        "d+" : this.getDate(),                    //日   
        "h+" : this.getHours(),                   //小时   
        "m+" : this.getMinutes(),                 //分   
        "s+" : this.getSeconds(),                 //秒   
        "q+" : Math.floor((this.getMonth()+3)/3), //季度   
        "S"  : this.getMilliseconds()             //毫秒   
      };   
      if(/(y+)/.test(fmt))   
        fmt=fmt.replace(RegExp.$1, (this.getFullYear()+"").substr(4 - RegExp.$1.length));   
      for(var k in o)   
        if(new RegExp("("+ k +")").test(fmt))   
      fmt = fmt.replace(RegExp.$1, (RegExp.$1.length==1) ? (o[k]) : (("00"+ o[k]).substr((""+ o[k]).length)));   
      return fmt;   
    }  
```
调用方法：
```
var time1 = new Date().format("yyyy-MM-dd HH:mm:ss");     
      
var time2 = new Date().format("yyyy-MM-dd");    
```
### 方法二：
```
/**       
 * 对Date的扩展，将 Date 转化为指定格式的String       
 * 月(M)、日(d)、12小时(h)、24小时(H)、分(m)、秒(s)、周(E)、季度(q) 可以用 1-2 个占位符       
 * 年(y)可以用 1-4 个占位符，毫秒(S)只能用 1 个占位符(是 1-3 位的数字)       
 * eg:       
 * (new Date()).pattern("yyyy-MM-dd hh:mm:ss.S") ==> 2006-07-02 08:09:04.423       
 * (new Date()).pattern("yyyy-MM-dd E HH:mm:ss") ==> 2009-03-10 二 20:09:04       
 * (new Date()).pattern("yyyy-MM-dd EE hh:mm:ss") ==> 2009-03-10 周二 08:09:04       
 * (new Date()).pattern("yyyy-MM-dd EEE hh:mm:ss") ==> 2009-03-10 星期二 08:09:04       
 * (new Date()).pattern("yyyy-M-d h:m:s.S") ==> 2006-7-2 8:9:4.18       
 */          
Date.prototype.pattern=function(fmt) {           
    var o = {           
    "M+" : this.getMonth()+1, //月份           
    "d+" : this.getDate(), //日           
    "h+" : this.getHours()%12 == 0 ? 12 : this.getHours()%12, //小时           
    "H+" : this.getHours(), //小时           
    "m+" : this.getMinutes(), //分           
    "s+" : this.getSeconds(), //秒           
    "q+" : Math.floor((this.getMonth()+3)/3), //季度           
    "S" : this.getMilliseconds() //毫秒           
    };           
    var week = {           
    "0" : "/u65e5",           
    "1" : "/u4e00",           
    "2" : "/u4e8c",           
    "3" : "/u4e09",           
    "4" : "/u56db",           
    "5" : "/u4e94",           
    "6" : "/u516d"          
    };           
    if(/(y+)/.test(fmt)){           
        fmt=fmt.replace(RegExp.$1, (this.getFullYear()+"").substr(4 - RegExp.$1.length));           
    }           
    if(/(E+)/.test(fmt)){           
        fmt=fmt.replace(RegExp.$1, ((RegExp.$1.length>1) ? (RegExp.$1.length>2 ? "/u661f/u671f" : "/u5468") : "")+week[this.getDay()+""]);           
    }           
    for(var k in o){           
        if(new RegExp("("+ k +")").test(fmt)){           
            fmt = fmt.replace(RegExp.$1, (RegExp.$1.length==1) ? (o[k]) : (("00"+ o[k]).substr((""+ o[k]).length)));           
        }           
    }           
    return fmt;           
}         
       
var date = new Date();        
window.alert(date.pattern("yyyy-MM-dd hh:mm:ss"));  
```

### 方法三：

```
Date.prototype.format = function(mask) {        
       
    var d = this;        
       
    var zeroize = function (value, length) {        
       
        if (!length) length = 2;        
       
        value = String(value);        
       
        for (var i = 0, zeros = ''; i < (length - value.length); i++) {        
       
            zeros += '0';        
       
        }        
       
        return zeros + value;        
       
    };          
       
    return mask.replace(/"[^"]*"|'[^']*'|/b(?:d{1,4}|m{1,4}|yy(?:yy)?|([hHMstT])/1?|[lLZ])/b/g, function($0) {        
       
        switch($0) {        
       
            case 'd':   return d.getDate();        
       
            case 'dd':  return zeroize(d.getDate());        
       
            case 'ddd': return ['Sun','Mon','Tue','Wed','Thr','Fri','Sat'][d.getDay()];        
       
            case 'dddd':    return ['Sunday','Monday','Tuesday','Wednesday','Thursday','Friday','Saturday'][d.getDay()];        
       
            case 'M':   return d.getMonth() + 1;        
       
            case 'MM':  return zeroize(d.getMonth() + 1);        
       
            case 'MMM': return ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'][d.getMonth()];        
       
            case 'MMMM':    return ['January','February','March','April','May','June','July','August','September','October','November','December'][d.getMonth()];        
       
            case 'yy':  return String(d.getFullYear()).substr(2);        
       
            case 'yyyy':    return d.getFullYear();        
       
            case 'h':   return d.getHours() % 12 || 12;        
       
            case 'hh':  return zeroize(d.getHours() % 12 || 12);        
       
            case 'H':   return d.getHours();        
       
            case 'HH':  return zeroize(d.getHours());        
       
            case 'm':   return d.getMinutes();        
       
            case 'mm':  return zeroize(d.getMinutes());        
       
            case 's':   return d.getSeconds();        
       
            case 'ss':  return zeroize(d.getSeconds());        
       
            case 'l':   return zeroize(d.getMilliseconds(), 3);        
       
            case 'L':   var m = d.getMilliseconds();        
       
                    if (m > 99) m = Math.round(m / 10);        
       
                    return zeroize(m);        
       
            case 'tt':  return d.getHours() < 12 ? 'am' : 'pm';        
       
            case 'TT':  return d.getHours() < 12 ? 'AM' : 'PM';        
       
            case 'Z':   return d.toUTCString().match(/[A-Z]+$/);        
       
            // Return quoted strings with the surrounding quotes removed        
       
            default:    return $0.substr(1, $0.length - 2);        
       
        }        
       
    });        
       
};   
```