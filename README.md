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
$ npm install -g yo generator-webapp gulp bower
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
$ cd app && npm init
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
gulp-install追加
```
npm install --save gulp-install
```

gulpタスクの追加
```
// Install npm module
var install = require("gulp-install");

gulp.task('install', function() {
  gulp.src(['./app/bower.json', './app/package.json'])
    .pipe(gulp.dest('./dist'))
    .pipe(install());
});

gulp.task('build', ['lint', 'html', 'images', 'fonts', 'extras', 'install'], () => {
  return gulp.src('dist/**/*').pipe($.size({title: 'build', gzip: true}));
});
```

Procfile

```
web: node web.js
```

Git

```
$ cd dist
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
 
gulp.task('deploy_github', function() {
  return gulp.src('./dist/**/*')
    .pipe(ghPages());
});
```

```
$ gulp deploy_github
```

#### AWSのセットアップ
開発用仮想マシンにログイン
```
$ vagrant up
$ vagrant ssh
$ cd /vagrant
```

gulpタスクの追加
```
$ npm install --save-dev gulp-s3
```
_gulpfile.js_

```
// Deploy AWS S3
var fs = require('fs');
var s3 = require("gulp-s3");

gulp.task('deploy_aws', function() {
  aws = JSON.parse(fs.readFileSync('./aws.json'));
  gulp.src('./dist/**')
    .pipe(s3(aws));
});
```

S3バケットデプロイ用ユーザーを作成して以下のポリシーを適用する
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:*",
            "Resource": [
                "arn:aws:s3:::demo.hiroshima-arc.org",
                "arn:aws:s3:::demo.hiroshima-arc.org/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": "s3:ListAllMyBuckets",
            "Resource": "*"
        }
    ]
}
```

aws.jsonにクレデンシャルを設定してバケットを作成する
```
$ aws s3 mb s3://demo.hiroshima-arc.org
```

[バケットの設定をする](https://docs.aws.amazon.com/ja_jp/gettingstarted/latest/swh/getting-started-configure-bucket.html)

以下のバケットポリシーを適用する
```
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "Allow Public Access to All Objects",
			"Effect": "Allow",
			"Principal": "*",
			"Action": "s3:GetObject",
			"Resource": "arn:aws:s3:::demo.hiroshima-arc.org/*"
		}
	]
}
```

デプロイを実行する

```
$ gulp deploy_aws
```

Route53で以下のドメインを設定したらアクセスできるか確認する

http://demo.hiroshima-arc.org

## 参照
+ [現場のプロが本気で教える HTML/CSSデザイン講義](https://www.amazon.co.jp/dp/B01K3SZGR0/ref=dp-kindle-redirect?_encoding=UTF8&btkr=1)
+ [現場のプロが本気で教える HTML/CSSデザイン講義 サンプルデータ](https://github.com/basara669/html_css_book)
+ [Launching apps with Yeoman](https://medium.com/console-log-yo/launching-apps-with-yeoman-1d0dfa627305#.dbvyvn8mu)
+ [Gulp-gh-pages](http://yeoman.io/learning/deployment.html) 
+ [sanitize.css](https://github.com/jonathantneal/sanitize.css) 
+ [flipsnap.jp](http://hokaccha.github.io/js-flipsnap/)
+ [Underscore.js](http://underscorejs.org/)
+ [Font Awesome](https://github.com/components/font-awesome)
+ [Google Fonts](https://fonts.google.com/)
+ [アマゾン ウェブ サービスで静的ウェブサイトをホストする](https://docs.aws.amazon.com/ja_jp/gettingstarted/latest/swh/website-hosting-intro.html)
