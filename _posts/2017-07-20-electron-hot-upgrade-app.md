---
id: 566
title: 'Electron实现在线升级 热更新 遇到的问题'
date: '2017-07-20T11:32:09+08:00'
author: wenjie
layout: post
guid: 'https://javascript.shop/?p=566'
permalink: /2017/07/electron-hot-upgrade-app
categories:
    - Javascript
---

一个项目正式交付客户使用了，对于版本的升级和代码更新是个一直困扰的问题，由于之前一直都是内部人员使用，可以让他们每次都下载完整压缩包（压缩后大概 40-50MB），但对于正式的终端用户，这个体验是不可接受。  
参考过 Electron [官方文档](https://link.juejin.im?target=https%3A%2F%2Felectronjs.org%2Fdocs%2Fapi%2Fauto-updater)，里面提及的方案兼容性并不好（Linux 不支持），需要准备的东西不少，而且交互上也不满意；本文介绍方案的细节及中间遇到的一些坑。

### 流程图

<figure class="wp-block-image">![processon](https://user-gold-cdn.xitu.io/2019/3/15/16980382b41a5126?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)</figure>
1. 通过 `app.getVersion()` 获取到 App 的本地版本号，这个版本号来源于 `package.json`；
2. 获取线上的 App 版本信息，此处我们设计了两个版本信息（如图例子）；  
    2.1. “1.3.0” 为核心版本号，该版本号用于判断是否需要重新下载完整包（图中所示的对应不同平台有不同的下载地址，为 zip 扩展名的压缩包，至于为什么用 zip，下文会提及），比如当 Electron 核心版本升级，或者增加了 node\_modules 依赖等情况；  
    2.2. “1.3.3” 为热更新版本号，这种情况只需要更新特定的文件列表（图中所示的 upgrade\_1.3.3.tar.gz），通常包含 `package.json`、`src/*` 等；  
    2.3. `description` 分别表示核心版本和热更新版本的描述；
3. 对比本地版本号 (`appVersion`) 和线上的版本号 (`onlineVersion`)，下面是版本号比较的代码：

```
<pre class="wp-block-preformatted">/* v1 为本地版本号，v2 为线上版本号 */
const compareVersion = (v1, v2) => {
  const vs1 = v1.toString().split('.'), vs2 = v2.toString().split('.');
  if (vs1.length !== vs2.length) {
    // 版本格式不一致
    return true;
  }
  for (let i = 0; i < vs1.length; ++i) {
    let diff = parseInt(vs2[i], 10) - parseInt(vs1[i], 10);
    if (diff < 0) {
      // vs1 其中一个版本号段小于 vs2
      return false;
    }
    if (diff > 0) {
      // vs2 其中一个版本号段大于 vs1
      return true;
    }
  }
  // 版本一致
  return false;
};
```

1. 3.1. 对比本地版本号和线上核心版本号 (`onlineVersion.version`)，如果小于线上版本号，则激活浏览器并进入下载，同时退出 App；  
    3.2. 否则进行对比本地版本号和热更新版本号 (`onlineVersion.upgrade.version`)，如果小于线上版本号，则下载热更新包；
2. 根据 (`upgrade.url`) 下载热更新包、升级、重启 App，全部代码：

```
<pre class="wp-block-preformatted">const { app } = require('electron'),
    fs = require('fs-extra'),  // 用于扩展内置 fs 方法
    request = require('request'),  // 用于发起下载请求
    tar = require('tar');  // 用于执行 tar 解压缩
 
//...
 
// 下载热更新包代码
new Promise((resolve, reject) => {
  request({
    url: onlineVersion.upgrade.url,
    encoding: null  // encoding 为 null 使 body 生成为一个 Buffer
  }, (error, res, body) => {
    try {
      if (error || res.statusCode !== 200) {
        throw '请求失败';
      }
      // 保存到临时目录，temp 为 Electron 用户可写目录
      let tempPath = app.getPath('temp');
      let dir = fs.mkdtempSync(`${tempPath}/upgrade_`);
      // 创建 Buffer 流并解压
      let stream = new require('stream').Readable();
      stream.push(body);
      stream.push(null);
      stream.pipe(tar.extract({
        sync: true,
        cwd: dir
      })).on('close', () => {
        // 解压完毕，复制更新文件
        fs.copySync(dir, __dirname);  // 解压至指定的目录，这里用 __dirname 为例
        fs.removeSync(dir);  // 删除临时目录
        // 返回 true 表示需要重启
        resolve(true);
      });
    } catch (e) {
      reject('更新文件下载失败，请联系管理员');
    }
  }
}).then(result => {
  if (result) {
    app.relaunch({ args: process.argv.slice(1) });  // 重启
    app.exit(0);
  }
}).catch(e => {
  // e 错误
});

作者：玉
链接：https://juejin.im/post/5c8b51fa5188257e8e232d13
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 坑

1. Windows 下可以直接把更新文件保存到 App 目录（没有权限问题），而在 Mac OSX 下，App 目录是无权写入的，而临时文件路径必须通过 [app.getPath()](https://link.juejin.im?target=https%3A%2F%2Felectronjs.org%2Fdocs%2Fapi%2Fapp%23appgetpathname) 来获取，常用 `temp` 或者 `userData` 目录来保存解压文件；
2. 压缩包我使用了 zip 一个原因是 Windows 下默认就能支持 zip 包解压，另一个主要原因是文件名编码问题，最初用 tar 来压缩文件，对于英文文件名是正常的，对于中文文件名，在 Mac OSX 下打包后在 Windows 是无法被正确解开的。而貌似 7z 生成的 zip 包会保存文件名字符集并能被 Windows 正确解开，所以在 gulp 中使用了 7z 来产生 zip 压缩包：

```
<pre class="wp-block-preformatted">const child = require('child_process');
child.exec(`7z a -tzip -r ./app-{platform}_${packageInfo.version}.zip ./中文的应用程序名-${platform}`, (error, stdout, stderr) => {
  //...
});
```

而对于热更新包，由于源代码都是使用英文名，所以用 tar 来压缩没毛病：

```
<pre class="wp-block-preformatted">const tar = require('tar');
tar.create({
  gzip: true,
  file: `./upgrade_${packageInfo.version}.tar.gz`,
  cwd: './build'
}, [ 'package.json', 'src' ]).then(() => {  // 保存 package.json 和 src 目录
  //...
});
```