title: 利用gulp压缩hexo
tags: hexo
date: 2020-02-21 23:50:30
---
![](http://cdn.mucjustin.cn/blog/2020-02-21-WechatIMG5.jpeg)

<!--more-->

#  1.安装相关插件

``` bash
#全局安装taobao cnpm
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

``` bash
#进入博客根目录
cd myblog
cnpm install gulp --save
cnpm install gulp-minify-css --save
cnpm install gulp-uglify --save
cnpm install gulp-htmlmin --save
cnpm install gulp-htmlclean --save
cnpm install gulp-imagemin --save
```

``` bash
# 安装babel
cnpm install babel-core@6.26.3 --save
cnpm install gulp-babel@7.0.1 --save
cnpm install babel-preset-es2015@6.24.1 --save
# gulp-babel 取消严格模式方法
cnpm install babel-plugin-transform-remove-strict-mode --save
```

# 2.gulp降级

退回3.9.1版本

``` bash
npm install --save-dev gulp@3.9.1
npm install -g gulp@3.9.1
```

# 3.添加gulpfile.js配置文件

在 Hexo 站点下新建`gulpfile.js`文件，文件内容如下

``` bash
var gulp = require('gulp');
var minifycss = require('gulp-minify-css');
var uglify = require('gulp-uglify');
var htmlmin = require('gulp-htmlmin');
var htmlclean = require('gulp-htmlclean');
var imagemin = require('gulp-imagemin');
var babel = require('gulp-babel');

// 压缩css文件
gulp.task('minify-css', function (done) {
    return gulp.src('./public/**/*.css')
        .pipe(minifycss())
        .pipe(gulp.dest('./public'));
    done();
});

// 压缩html文件
gulp.task('minify-html', function (done) {
    return gulp.src('./public/**/*.html')
        .pipe(htmlclean())
        .pipe(htmlmin({
            removeComments: true,
            minifyJS: true,
            minifyCSS: true,
            minifyURLs: true,
        }))
        .pipe(gulp.dest('./public'));
    done();
});

// 压缩js文件
gulp.task('minify-js', function (done) {
    return gulp.src(['./public/**/*.js', '!./public/**/*.min.js'])
        .pipe(babel({
            //将ES6代码转译为可执行的JS代码
            presets: ['es2015'] // es5检查机制
        }))
        .pipe(uglify())
        .pipe(gulp.dest('./public'));
    done();
});

// 压缩 public/images 目录内图片(Version<3)
 gulp.task('minify-images', function () {
     gulp.src('./public/images/**/*.*')
         .pipe(imagemin({
             optimizationLevel: 5, //类型：Number  默认：3  取值范围：0-7（优化等级）
             progressive: true, //类型：Boolean 默认：false 无损压缩jpg图片
             interlaced: false, //类型：Boolean 默认：false 隔行扫描gif进行渲染
             multipass: false, //类型：Boolean 默认：false 多次优化svg直到完全优化
         }))
         .pipe(gulp.dest('./public/images'));
 });



//4.0以前的写法 
gulp.task('default', [
  'minify-html', 'minify-css', 'minify-js', 'minify-images'
]);
```

# 4.创建.babelrc文件

在 Hexo 站点下新建`.babelrc`文件，文件内容如下：

``` bash
{
    "presets": ["es2015"],
    "plugins": ["transform-remove-strict-mode"]
}
```

# 5.给node降级

用来适配gulp3.9.1

``` bash
npm install -g n

sudo n 11.15.0

npm install gulp@^3.9.1
npm install 
npm rebuild node-sass
```

# 6.执行压缩静态资源

``` bash
hexo g
gulp
hexo d
```