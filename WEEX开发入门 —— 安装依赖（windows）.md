## WEEX开发入门 -安装依赖-windows

#### 一、前言

**2018-10-24** 在这个圣神的日子，我正式开始了weex的开发之旅

#### 二、安装Node.js和npm

```
$ node -v
v8.11.1
$ npm -v
5.6.0
```

接下来使用 npm 来安装 weex-toolkit

#### 三、安装weex-toolkit

```
$ npm install -g weex-toolkit

//完成后
$ weex -v //查看当前weex版本

//我在这里有个报错
Warning: npm 5 is not supported yet
//只好将npm版本改为4
npm i npm@4 -g  //若无法修改请看 四、若在windows下，npm5.6.0版本无法修改

//再次查看当前weex版本
$ weex -v
```

**注意: **在`weex-toolkit`1.0.8版本后添加了npm5规范的`npm-shrinkwrap.json`用于锁定包依赖，故npm版本<5的需要通过`npm i npm@latest -g`更新一下npm的版本。

**提示: **如果提示权限错误（*permission error*），使用 `sudo` 关键字进行安装

```
$ sudo cnpm install -g weex-toolkit
```

#### 四、若在windows下，npm5.6.0版本无法修改

```
node -v
v8.11.4
npm -v
5.6.0

//也可以直接文件夹打开node安装目录，手动重命名
cd %ProgramFiles%\nodejs
ren npm.cmd npm2.cmd
ren npm npm2
ren npx npx2
ren npx npx2.cmd
//end

//下载你需要的版本
npm2 install npm@4 -g
//也可以不用删掉
del npm2
del npm2.cmd
del npx2
del npx2.cmd

node -v
v8.11.4
npm -v
6.4.1
npm2 -v
5.6.0
```

#### 五、使用Android Studio打开WEEX项目

默认情况下，`weex create`命令并不初始化`Android`和`IOS`项目，所以需要执行`weex platform add`来添加特定平台的项目

```
//对应项目文件中打开cmd

weex platform add android //加载android模块
weex run android          //然后启动
```

注意：`Android Studio`打开项目文件中`\platform\android`文件夹，运行即可

若有其他版本兼容问题，参考 https://www.jianshu.com/p/b106a7bb4437