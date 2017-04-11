`action`
    一个处理这个form信息的程序所在的URL.这个值可以被`<button>`或者`<input>`的`formaction`属性重载.

---

`autocomplete`
 用于指示`input`元素是否能够拥有一个默认值，这个默认值由浏览器自动补全。这个设定可以被这个form的子元素的`autocomplete`属性重载。
 - off: 浏览器不会自动补全
 - on: 浏览器能够根据用户之前在form里输入的值自动补全。

---

`enctype`
当`method`属性值位`post`时，`enctype`是提交form给服务器的内容的`MIME类型`。
- `application/x-www-form-urlencoded`: 默认值
- `multipart/form-data`: 这个值用于一个`type`属性设置为`file`的`<input>`元素。
- `text/plain` (HTML5)
这个值可以被`<button>`或者`input`元素中的`formencvtype`属性重载(覆盖)。

---

`method`
浏览器使用这种`HTTP`方式来提交`form`.可能的值有：
- `post`
- `get`
这个值可以被 `<button>` 或者 `<input>` 元素中的 `formmethod` 属性重载（覆盖）。

---

`name`
`form`的名字。在HTML5中，这个`name`是唯一的而不仅仅是一个空字符串。

---

`novalidate`
布尔型。指示了`form`是否没有验证。没有指定，这个`form`是验证通过的。


































