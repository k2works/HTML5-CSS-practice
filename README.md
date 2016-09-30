# HTML5-CSS-practice

## 目的
HTML/CSSデザイン講義の内容を実践する

## 構成
+ セットアップ
+ アプリケーションデプロイセットアップ

### セットアップ
#### 必要なパッケージをインストールする
```
$ vagrant up
$ vagrant ssh
$ cd /vagrant
$ npm install -g yo generator-webapp gulp
```
#### アプリケーションのベースをスキャッフォルドで生成する
```
$ yo webapp
$ gulp
$ gulp serve 
```
[http://localhost:9009](http://localhost:9009)に接続して動作を確認する。

### アプリケーションデプロイセットアップ
#### Herukuのセットアップ
```
$ cd dist && npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg> --save` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
name: (dist) demo-web-site
version: (1.0.0) 
description: 
entry point: (index.js) web.js
test command: 
git repository: 
keywords: 
author: 
license: (ISC) 
About to write to /vagrant/dist/package.json:

{
  "name": "demo-web-site",
  "version": "1.0.0",
  "description": "",
  "main": "web.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}


Is this ok? (yes) yes
```
Express & Gzippo

```
$ npm install express morgan gzippo --save
```

web.js

```
var express = require('express');
var http = require('http');
var gzippo = require('gzippo');
var logger = require('morgan');
var app = express();
app.use(logger());
app.use(gzippo.staticGzip('' + __dirname));
var server = http.createServer(app);
server.listen(process.env.PORT || 5000);
```

Procfile

```
web: node web.js
```

Git

```
$ git init
$ git add -A
$ git commit -m 'Initial Commit'
```
Heroku

```
$ heroku create demo-web-site
$ git push heroku master
$ heroku domains
$ heroku destroy demo-web-site
```
#### GitHub Pageのセットアップ
gulp-gh-pagesのインストールす

```
$ /vagratn/
$ npm install --save-dev gulp-gh-pages
```

gulpタスクの追加
```
// Deply GitHub Page
var ghPages = require('gulp-gh-pages');
 
gulp.task('deploy', function() {
  return gulp.src('./dist/**/*')
    .pipe(ghPages());
});
```

```
$ gulp deploy
```


## 参照
+ [現場のプロが本気で教える HTML/CSSデザイン講義](https://www.amazon.co.jp/dp/B01K3SZGR0/ref=dp-kindle-redirect?_encoding=UTF8&btkr=1)
+ [Launching apps with Yeoman](https://medium.com/console-log-yo/launching-apps-with-yeoman-1d0dfa627305#.dbvyvn8mu)
+ [Gulp-gh-pages](http://yeoman.io/learning/deployment.html) 
