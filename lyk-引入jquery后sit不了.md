### 1.项目最开始sit总显示模块加载错误

引入了element-ui和jquery。

将依赖包删了重新 npm i 一次,运行dev不报错结果打开项目控制台报错webpakeJsonp is not defined

可以到build->webpack.base.conf.js里面把引入jquery时加入的 plugins注释掉

```javascript
module.exports = {
  ...,
  
  module: {
    rules: [
      ...,
  
      {
        test: /\.scss$/,
        loaders: ["style", "css", "sass"]
      },
      {
        test: /\\\\\\\\.css$/,
        loader: "style!css"
      },
      {
        test: /\\\\\\\\.(eot|woff|woff2|ttf)([\\\\\\\\?]?.*)$/,
        loader: "file"
      }
    ]
  },
  // plugins: [
  //   new webpack.optimize.CommonsChunkPlugin('common.js'),
  //   new webpack.ProvidePlugin({
  //     jQuery: "jquery",
  //     $: "jquery"
  //   })
  // ],
}
```

去到项目里面的main.js

```javascript
import Vue from 'vue'
import App from './App'
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
import $ from 'jquery'

Vue.use(ElementUI)
/* eslint-disable no-new */
require('es6-promise').polyfill()
Vue.prototype.$=$
new Vue({
  el: '#hr-system-index',
  template: '<App/>',
  components: { App }
})
```

然后在你需要引用jquery的vue文件里面添加

```javascript
import $ from 'jquery'
```

