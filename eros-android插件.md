## eros android插件

#### 一、前言

这里我使用jitpack第三方依赖库

#### 二、jitpack发布流程

1、插件代码与github关联

* github申请仓库，clone后用Android Studio打开

* 项目的根目录的build.gradle里面需要配置

  ```
  buildscript{
  	...
      dependencies {
      	classpath 'com.github.dcendents:android-maven-gradle-plugin:1.3'
      }
  }

  //如果配置后同步发现错误，把上面的版本1.3换成1.5
  ```

* 创建module

  1、直接在项目中new 一个Moudle，没有特别要求，按照默认的点下去

  ![img](https://raw.githubusercontent.com/myliuyx/source/master/plugin_new_1.jpg)

  2、将新创建的 Moudle 依赖进项目

  ![img](https://raw.githubusercontent.com/myliuyx/source/master/plugin_new_5.jpg)

  3、修改 Moudle 的 build.gradle 文件，依赖： `java implementation 'com.github.bmfe.eros-nexus:nexus:1.0.1'`。依赖请参考最新版本 自行修改

  ![img](https://raw.githubusercontent.com/myliuyx/source/master/plugin_new_6.png)

  4、后面您可以随意写您的插件逻辑了， 集体 的注册可以参考 `simple` 中的 ErosPluginSimple

* 最后完成代码即可，提交到GitHub仓库去

#### 三、GitHub release产生版本

1、在插件项目主页上，点击release，进入release界面

![å¦å¾](https://img-blog.csdn.net/20160806212058689)

2、在release界面点选create a new release

![ç¹å» create a new release](https://img-blog.csdn.net/20160806212252504)

3、填写相关信息，上传文件，提交release版本到jitpack

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20160806212913205)

#### 四、查看[jitpack](https://link.jianshu.com/?t=https://www.jitpack.io/)官网

* 拷贝GitHub上仓库地址，**只拷贝到域名为仓库地址那一级**。 例：`lykufo007/AuxLiWeather`
* 将拷贝的地址粘贴到搜索框，点击Lookup就会找到我们的项目，在搜索的列表里面点击get it,就会在页面出现如何使用仓库的说明以及添加徽章

#### 五、使用我们创建好的仓库

* 根目录build.gradle添加

  ```
  allprojects {
          repositories {
              ...
              maven { url 'https://www.jitpack.io' }
          }
      }
  ```

* 在App的build.gradle里面添加dependencies

  ```
  dependencies {
              compile 'com.github.JackZhous:JMediaControl:v1.0'
      }
  ```



有其他问题，参考

https://www.jianshu.com/p/deab01fe5d79

https://github.com/bmfe/android-eros-plugin-simple