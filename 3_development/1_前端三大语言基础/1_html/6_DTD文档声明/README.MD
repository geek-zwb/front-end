# DTD文档声明
> DTD: Document Type Definition

`<!DOCTYPE>`声明必须位于 HTML5 文档中的第一行，也就是位于 `<html>` 标签之前。该标签告知浏览器文档所使用的 HTML 规范。

在html5 中， 如果没有 `<!DOCTYPE html>` ，浏览器将不能识别它是HTML文档，HTML5的标签将不能正常工作。

`doctype` 声明不属于 HTML 标签(tag); 它是一条指令，告诉浏览器编写页面所用的标记的版本

HTML 4.01 中的 doctype 需要对 DTD 进行引用，因为 HTML 4.01 基于 SGML。而 HTML 5 不基于 SGML，因此不需要对 DTD 进行引用，但是需要 doctype 来规范浏览器的行为（让浏览器按照它们应该的方式来运行。）。

SGML（标准通用标记语言）是一个标准，告诉我们怎么去指定文档标记。他是只描述文档标记应该是怎么样的元语言，HTML是被用SGML描述的标记语言。

![SGML](https://github.com/geek-zwb/front-end/blob/master/3_development/1_%E5%89%8D%E7%AB%AF%E4%B8%89%E5%A4%A7%E8%AF%AD%E8%A8%80%E5%9F%BA%E7%A1%80/1_html/6_DTD%E6%96%87%E6%A1%A3%E5%A3%B0%E6%98%8E/SGML.bmp)

### 通用声明

HTML5
```
<!DOCTYPE html>
```

HTML 4.01
```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">
```

XHTML 1.0
```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
```

> 当前主流浏览器都已经支持 HTML5. 所以按HTML5规范即可。