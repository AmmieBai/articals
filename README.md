# 20160502如何结合Gulp使用PostCss
## 作者钉钉用户名：
## @Ammie Bai

# **封面配图**

![MacDown logo](http://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2016/03/1459366991postcss-screen.jpg)

# **文章摘要**

>PostCSS 是使用 JS 插件来转换 CSS 的工具，支持变量，混入，未来 CSS 语法，内联图像等等。PostCSS的目标是通过自定义插件和工具这样的生态系统来改造CSS。与Sass和Less这些预编译器相同的原则，PostCSS把扩展的语法和特性转换成现代的浏览器友好的CSS。使用Gulp工具，我们可以通过构建过程转换样式，就像Sass和Less的编译。

# **文章正文**

> [PostCSS](http://postcss.org/)现在已经普及了一段时间，如果您还没有使用过它或不知道PostCSS是什么，那么我建议您看看这篇[PostCSS简介](http://www.sitepoint.com/an-introduction-to-postcss/)，该文章介绍了PostCSS的基础知识，包括如何安装和使用一些PostCSS插件。

> 在本教程中，我将向您展示如何结合Gulp使用PostCSS，一个流行的自动化工具。由于这篇文章不是一个关于Gulp的入门教程，所以我不会涵盖[Gulp](http://gulpjs.com/)的基础知识。但为了快速入门，您可以看看这篇[Gulp.js简介](http://www.sitepoint.com/introduction-gulp-js/)。

## 1.项目设置

>在开始本教程之前，你应该创建一个项目文件夹用来工作。该文件夹内应该已经安装了Gulp以及两个内部文件夹，名称分别为“initial”和“final”（或您选择的其它名称）。所谓的“initial”的文件夹将存放你的原材料和未加工的CSS代码。 “initial”文件夹将存放处理过的文件，随时可以使用。

>在继续之前，在您的项目文件夹中打开使用终端，运行以下命令：

	npm install gulp-postcss --save-dev       
> **PS：** --save-dev补充：表示将正在安装的插件作为依赖添加到project.json文件里。这将对多人合作模式有很大的帮助。当其他开发人员运行`npm install`在软件包安装命令，所有的插件将被自动安装。

>此时你的文件夹结构应该是：
>
  * initial —您的原始CSS文件的文件夹。
>
   * style.css —未处理的css样式，我们将在稍后进行编辑。
  * final — 处理过的css文件的文件夹
  * node_modules — 所有NPM模块的文件夹
>
   * gulp — 当你安装Gulp时创建的文件夹
   * gulp-postcss —运行上面的命令后创建的文件夹。
  * guplfile.js —你的 Gulp文件
  * package.json —你的 package.json 文件

## 2.安装插件

> 开始之前，我们先安装一些基本的插件。我们将要使用的插件是 [short-color](https://github.com/jonathantneal/postcss-short-color)（一个颜色插件）.这个插件基本上扩展了现有的颜色属性，并且可以设置第二个颜色值(将会被作为背景颜色)。运行以下命令来安装 short-color。

	npm install postcss-short-color --save-dev
                          
> 你还可以同时安装使用gulp-postcss和postcss-short-color ：

	npm install gulp-postcss postcss-short-color --save-dev
                 
> 这两个插件安装完毕后，你需要打开并编辑gulpfile.js文件，这样就可以开始使用该插件工作。我们首先通过以下代码来启用这两个插件：

	var gulp = require('gulp');
	
	var postcss = require('gulp-postcss');
	
	var shortColor = require('postcss-short-color');

> 现在，让我们建立一个gulp任务来处理我们的原始的CSS文件，并创建一个准备就绪的样式表。下面是代码这样做：

	gulp.task('css', function () {
	  return gulp.src('initial/*.css')
	  .pipe(postcss([shortColor]))
	  .pipe(gulp.dest('final'));
	});

> 我首先创建一个名为CSS的任务。当你要运行相应的功能时可以用到这个名字。您可以在gulp.src()内部指定要处理的文件。使用*的CSS将处理“intial”文件夹内所有的CSS文件。

> 接下来，我们使用pipe()函数来调用我们的所有插件。要做到这一点，我们需要将所有的插件作为参数传递给postcss()函数。在我们的基本的例子中，我们实际上只有一个插件需要传递。 在下一节，我会告诉你如何传递多个插件。插件通过管道后，gulp.dest()将被用于设置处理的文件的目的地。

> 为了测试，如果一切已正确设置，在“initial”文件夹内创建一个style.css文件。 style.css文件将有以下的CSS：

	section {
	  color: white black;
	}

>接下来，你需要在你的终端里运行`gulp css`命令。此刻，在您的“final”文件夹内，你应该会看到一个style.css文件，像下面的CSS 一样：

	section {
	  color: white;
	  background-color: black;
	}

> 如果您CSS的样式和上面所展示的CSS样式一样的话，我们就可以认为一切工作正常。
 
## 3.安装多个插件

> 只使用一个基本的插件并不能从根本上减轻你的工作量。PostCSS有很多令人印象深刻的且令人难以置信的有用的插件，所以在大多数情况下，你需要使用到的插件不止一个。在本节中，我将告诉你如何在工作中同时使用多个插件。

> 您可能会发现有三个PostCSS插件（包）是非常有用的：即 [short](https://github.com/jonathantneal/postcss-short), [cssnext](https://github.com/MoOx/postcss-cssnext) 和 [autoprefixer](https://github.com/postcss/autoprefixer)

> * short将使你少写并且速记大量的CSS属性。 例如:

	.heading {
	  font-size: 1.25em 2;
	}
> 将会被解析成：
	
	.heading {
	  font-size: 1.25em;
	  line-height: 2;
	}
	
> * cssnext能够在你的css样式表中，使用最新的CSS语法。例如，下面的CSS：

	.link {
	  color: color(green alpha(-10%));
	}
	
> 将被转换为：

	.link {
	  background: rgba(0, 255, 0, 0.9);
	}
	
> * 最后，autoprefixer将会在您的CSS和转换后的样式文件中添加供应商前缀：
	
	img {
	  filter: grayscale(0.9);
	}

> 转换为：

	img {
	  -webkit-filter: grayscale(0.9);
	  filter: grayscale(0.9);
	}

> 现在让我们看看如何安装这些插件，然后用它们来改造我们的样式表。在项目目录下运行下面的命令：

	npm install autoprefixer postcss-short postcss-cssnext --save-dev

> 我们需要修改我们的gulp task来包含所有这些插件，代替用管道将所有这些插件一个一个的传递，我们将它们存储在易于维护和简洁的阵列。下面是我们的gulpfile.js的代码：

	var gulp = require('gulp');
	var postcss = require('gulp-postcss');
	var autoprefixer = require('autoprefixer');
	var cssnext = require('postcss-cssnext');
	var shortcss = require('postcss-short');
	gulp.task('css', function() {
	  var plugins = [
	    shortcss,
	    cssnext,
	    autoprefixer({browsers: ['> 1%'], cascade: false})
	  ];
	  return gulp.src('initial/*.css')
	  .pipe(postcss(plugins))
	  .pipe(gulp.dest('final'));
	});

> 您可能已经注意到了，我已经指定了Autoprefixer插件的一些选项。Autoprefixer输出这些选项确定的CSS，在这种情况下，我说的是插件支持所有全球使用量超过1％的浏览器。我也被禁用级联，让所有的前缀和不带前缀属性左对齐。类似的选项也可用于大多数其他插件。
> 
> 为了测试，如果一切正常，将出在样式表下面的CSS：

	.prefix-filter {
	  display: flex;
	}
	  
	.cssshort-section {
	  text: #333 bold justify uppercase 1.25em 1.7 .05em;
	}
	.cssnext-link {
	  color: color(blue alpha(-10%));
	}
	.cssnext-link:hover {
	  color: color(orange blackness(80%));
	}
		
> 如果现在在终端中运行gulp css，你应该在“final”文件夹中可以得到style.css，如下：

	.prefix-filter {
	  display: -webkit-box;
	  display: flex;
	}
	  
	.cssshort-section {
	  color: #333;
	  font-weight: 700;
	  text-align: justify;
	  text-transform: uppercase;
	  font-size: 1.25em;
	  line-height: 1.7;
	  letter-spacing: .05em;
	}
	  
	.cssnext-link {
	  color: #0000ff;
	  color: rgba(0, 0, 255, 0.9);
	}
	  
	.cssnext-link:hover {
	  color: rgb(51, 33, 0);
	}

## ４.插件的执行顺序
> 在最后一节的例子很清楚说明了PostCSS的实用性。所有PostCSS的力量在于它的插件。

> 我们具体来研究两个插件 [rgba-fallback ](https://github.com/postcss/postcss-color-rgba-fallback)和 [color-function](https://github.com/postcss/postcss-color-function)， [rgba-fallback](https://github.com/postcss/postcss-color-rgba-fallback)插件会转换一个RGBA颜色为16进制格式，color-function将改变CSS的color()函数来兼容更多菜单CSS，比方说，你有一下的CSS：

	body {
	  background: color(orange a(90%));
	}

> 如果你运行的颜色功能插件之前的RGBA-后备插件，就像这样：
 
	 var plugins = [
	  rgbafallback,
	  colorfunction
	];
	
> 你将得到下面的CSS:

	body {
	  background: rgba(255, 165, 0, 0.9);
	}
	
> 正如你所看到的，在处理CSS时没有RGBA回退。这样的异常原因是回退插件运行时，它发现背景是color(orange a(90%))，并且不知道该怎么办。后来，color函数插件执行，并从原来的color函数创建RGBA颜色值。

> 如果你扭转插件顺序，按以下顺序运行它们：

	var plugins = [
	  colorfunction,
	  rgbafallback
	 ];
	 
> 最后的CSS是这样的：

	body {
	  background: #ffa500;
	  background: rgba(255, 165, 0, 0.9)
	}
	
> 这一次的颜色插件首先把背景色的RGBA值和后备插件层增加一个十六进制回退。

## 总结
> 在本教程中我已经介绍了使用gulp和PostCSS一起使用的基本知识。随着插件和gulp的正确组合，可以节省开发的许多时间和避免不必要的麻烦。如果您有任何技术问题或建议在本教程中提到让我知道在评论！

# 作者信息：

### 原文作者：Nitish Kumar

### 原文链接：<https://http://www.sitepoint.com/how-to-use-postcss-with-gulp/>

### 翻译自： MaxLeap 团队_前端研发人员：Ammie Bai

### 关键词信息：Gulp, task runner,PostCSS


