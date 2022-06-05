---
id: 523
title: NodeJS文件操作扩展fs-extra
date: '2019-08-17T23:15:15+08:00'
author: wenjie
layout: revision
guid: 'https://javascript.shop/522-revision-v1/'
permalink: '/?p=523'
---

 fs-extra模块是系统fs模块的扩展，提供了更多便利的API，并继承了fs模块的API，本文详细介绍所有操作方法，方便读者更好的操作服务端文件

## fs-extra安装与引入

github地址：<https://github.com/jprichardson/node-fs-extra>

| npm install –save fs-extra      const fs = require(‘fs-extra’); |
|---|

## fs-extra同步操作与异步操作介绍

| const fs = require(‘fs-extra’)      *// 异步两种操作方式*   *// Async with promises:*   fs.copy(‘/tmp/myfile’, ‘/tmp/mynewfile’)    .then(() =&gt; console.log(‘success!’))    .catch(err =&gt; console.error(err))      *// Async with callbacks:*   fs.copy(‘/tmp/myfile’, ‘/tmp/mynewfile’, err =&gt; {    if (err) return console.error(err)    console.log(‘success!’)   })      *// 同步两种操作方式*   *// Sync:*   try {    fs.copySync(‘/tmp/myfile’, ‘/tmp/mynewfile’)    console.log(‘success!’)   } catch (err) {    console.error(err)   }      *// Async/Await:*   async function copyFiles () {    try {    await fs.copy(‘/tmp/myfile’, ‘/tmp/mynewfile’)    console.log(‘success!’)    } catch (err) {    console.error(err)    }   }      copyFiles() |
|---|

## fs-extra各方法介绍

1. 异步文件操作方法

- copy 复制文件或文件夹
- emptyDir 清空文件夹（文件夹目录不删，内容清空）
- ensureFile 确保文件存在(文件目录结构没有会新建)
- ensureDir 确保文件夹存在(文件夹目录结构没有会新建)
- ensureLink 确保链接存在(链接目录结构没有会新建)
- ensureSymlink 确保符号链接存在(目录结构没有会新建)
- mkdirp 同ensureDir
- mkdirs 同ensureDir
- move 移动文件或文件夹
- outputFile 同fs.writeFile()，写文件(目录结构没有会新建)
- outputJson 写json文件(目录结构没有会新建)
- pathExists 判断文件是否存在
- readJson 读取JSON文件，将其解析为对象
- remove 删除文件或文件夹，类似rm -rf
- writeJson 将对象写入JSON文件。

1. 同步文件操作方法（异步方法名后面加上Sync即可）

- copySync
- emptyDirSync
- ensureFileSync
- ensureDirSync
- ensureLinkSync
- ensureSymlinkSync
- mkdirpSync
- mkdirsSync
- moveSync
- outputFileSync
- outputJsonSync
- pathExistsSync
- readJsonSync
- removeSync
- writeJsonSync

## fs-extra各方法举例（同步为例）

1. 复制

copySync(src, dest, \[options\])

| *// 复制文件或文件夹*      fs.copySync(‘/tmp/myfile’, ‘/tmp/mynewfile’)      *// 复制并覆盖目标目录*   fs.copySync(‘/tmp/mydir’, ‘/tmp/mynewdir’,overwrite) |
|---|

1. 清空文件夹

emptyDirSync(dir)

| *// 清空文件夹（文件夹目录不删，内容清空）*      fs.emptyDirSync(‘/tmp/some/dir’) |
|---|

1. 确保文件存在

ensureFileSync(file)

| *//确保文件存在。如果请求创建的文件在不存在的目录中，则会创建这些目录。如果文件已经存在，则不会被修改。*      const file = ‘/tmp/this/path/does/not/exist/file.txt’   fs.ensureFileSync(file) |
|---|

1. 确保文件夹存在

ensureDirSync(dir)

| // 确保目录存在。 如果目录结构不存在，则创建它。 像mkdir -p      const dir = ‘/tmp/this/path/does/not/exist’   fs.ensureDirSync(dir) |
|---|

1. 确保链接存在

ensureLinkSync(srcpath, dstpath)

| *// 确保链接存在。 如果目录结构不存在，则创建它。*      const srcpath = ‘/tmp/file.txt’   const dstpath = ‘/tmp/this/path/does/not/exist/file.txt’   fs.ensureLinkSync(srcpath, dstpath) |
|---|

1. 确保符号链接存在

ensureSymlinkSync(srcpath, dstpath, \[type\])

| *// 确保符号链接存在。 如果目录结构不存在，则创建它。*      const srcpath = ‘/tmp/file.txt’   const dstpath = ‘/tmp/this/path/does/not/exist/file.txt’   fs.ensureSymlinkSync(srcpath, dstpath) |
|---|

1. 移动文件或文件夹

moveSync(src, dest, \[options\])

| fs.moveSync(‘/tmp/somefile’, ‘/tmp/does/not/exist/yet/somefile’)      *//带覆盖功能*   fs.moveSync(‘/tmp/somedir’, ‘/tmp/may/already/existed/somedir’, { overwrite: true }) |
|---|

1. 写文件

outputFileSync(file, data, \[options\])

| *//与writeFileSync几乎相同（即覆盖），只要父目录不存在，就会创建它。文件必须是文件路径（不允许使用缓冲区或文件描述符）。选项与传递给fs.writeFileSync（）的选项相同*      const file = ‘/tmp/this/path/does/not/exist/file.txt’   fs.outputFileSync(file, ‘hello!’)      const data = fs.readFileSync(file, ‘utf8’)   console.log(data) *// =&gt; hello!* |
|---|

1. 写json文件

outputJsonSync(file, object, \[options\])

| *//与writeJsonSync几乎相同，只要目录不存在，就会创建它。*      const file = ‘/tmp/this/path/does/not/exist/file.json’   fs.outputJsonSync(file, *{name: ‘JP’}*)      const data = fs.readJsonSync(file)   console.log(data.name) *// =&gt; JP* |
|---|

1. 读取JSON文件，将其解析为对象

readJsonSync(file, \[options\])

| *//读取JSON文件，然后将其解析为对象。选项与传递给jsonFile.readFileSync的选项相同。*      const packageObj = fs.readJsonSync(‘./package.json’)   console.log(packageObj.version) *// =&gt; 2.0.0* |
|---|

1. 删除文件或文件夹

removeSync(path)

| *//删除文件或目录。 该目录可以包含内容。 同rm -rf。*   *// remove file*   fs.removeSync(‘/tmp/myfile’)      fs.removeSync(‘/home/jprichardson’) *// I just deleted my entire HOME directory.* |
|---|

1. 将对象写入JSON文件

writeJsonSync(file, object, \[options\])

| *// 将对象写入JSON文件*      fs.writeJsonSync(‘./package.json’, {name: ‘fs-extra’}) |
|---|

## fs-extra简单应用(NodeJS创建一个文件夹)

| const fse = require(‘fs-extra’);   const rootFileName = ‘root’;   const target\_dir = ‘targetDir/’;   const fileName = ‘abc’;   const fielType = ‘txt’;   const target\_dir = rootFileName + ‘/’;   const target\_path = target\_dir + fileName + ‘.’ + fileType;         *// 如果不存在的话创建文件夹*   if (!fs.existsSync(target\_dir)) {    fse.ensureDirSync(target\_dir);    console.log(‘Folder ‘ + target\_dir + ‘ has been created’);    } else {    console.log(‘Folder ‘ + target\_dir + ‘ is exist’);   } |
|---|