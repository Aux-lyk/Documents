### 一、项目背景

添加媒体的需求想要区分video和audio，tinymce自带tool的media功能，video可以依靠初始化属性`media_live_embds:true`（默认是true）来解决而audio并不能，audio在富文本框里面是img标签，在预览里面可以看到正常的audio。而我在前人已经将媒体添加功能重新封装过后，优化video和audio的插入。

注：我们项目引入tinymce是在index.html文件里面

`<script src=<%= htmlWebpackPlugin.options.path %>/tinymce4.7.5/tinymce.min.js></script>`

做此操作，下载的tinymce资源文件夹就放置static文件夹中

### 二、前人的积累	

在vue中，前人自己封装了一个引入了tinymce的子组件，在一个dialog里面输入地址，有添加视频和音频两个按钮用于区别添加。video用iframe的src属性添加就行。

```html
<template>
  <div>
    <textarea class="tinymce-textarea" :id="tinymceId" ref="myTinymce"></textarea>
    ...
  </div>
</template>
```

```javascript
//in methods

let html = `<iframe style="width:100%;min-height:200px;" frameborder="0" allowfullscreen="true" src="${
            this.videosrc
            }" ></iframe><br>`

window.tinymce
        .get(this.tinymceId)
        .insertContent(html);  //this.tinymceId就是绑定在显示富文本的html标签的id
```

### 三、audio插入优化

因为发现单纯的audio标签在文本框里面难以被删除，外面有一层iframe可以正常删除。而如果直接使用iframe会被显示为视频，无法控制autoplay、有黑色背景不美观。直接使用srcdoc属性则被tinymce把结果解析乱了。**问题明显是tinymce对insertContent的html元素的解析规则**。

看文档和翻墙后，发现初始化`valid_elements`属性，字面意思有效的元素。定义编辑器保存时编辑文本中将保留哪些元素，可以使用它将返回的HTML限制为子集。

```javascript
window.tinymce.init({
  ...,
  valid_elements:"*[*]", //所有元素的所有属性都解析。若想提升性能，看文档自定义自己需要的规则
  ...,
})
```

注：这里有个小坑

```javascript
//定义audio的html
let iframeHtml = `<audio controls>
              <source src="${this.videosrc}" type="audio/mp4">
              <source src="${this.videosrc}" type="audio/ogg">
              <source src="${this.videosrc}" type="audio/mpeg">
              <embed height="100" width="100" src="${this.videosrc}" />
              您的浏览器不支持 audio
            </audio>`
//定义需要插入的iframe的html
let html = `<iframe style="width: 100%;height:70px;" frameborder="0" allowfullscreen="true" allowTransparency="true" scrolling="no" srcdoc='${iframeHtml}' src="${this.videosrc}"></iframe><br>`
//使用srcdoc属性用单引号，否则tinymce在解析的时候会和iframeHtml里面的双引号形成字符串拼接
//导致你的iframeHtml解析出问题

```

