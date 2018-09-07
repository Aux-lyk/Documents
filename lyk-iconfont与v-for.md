

### 1、 iconfont的code在v-for中的使用

iconfont的code码在使用v-for时，不可直接写成内容，{{item.code}}。应当使用v-html=“item.code”