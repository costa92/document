布局容器

### 两种布局方式

1、 .container 类用于固定宽度并支持响应式布局的容器

```html
<div class="container"></div>
```
2、 .container-fluid 类用于 100% 宽度，占用全部视口（viewport） 的容器

```html
<div class="container-fluid"></div>
```

### 栅格网格系统

系统自动分割12个

container、row、xs(xsmall phones)、sm(small tablets)、md(middle desktops) 、lg(larger desktops)

xsmall phones: 超小屏（自动）

small tablets : 小屏（750px）

middle desktops : 中屏（970px）

larger desktops : 大屏（1170px）



数据行(.row) 必须放到容器中(.container)中，以便为其赋予合适的对齐的方式和内距(padding)

在行(.row) 中科院添加列(.column),只有列(column) 才可以作为容器(.row)的子元素，但列数之和不能超过平均的总列数。比如12 ，如果大于12 ，则自动换到下一行

##  媒体查询

```scss
/* 超小屏幕（手机，小于 768px） */
/* 没有任何媒体查询相关的代码，因为这在 Bootstrap 中是默认的（还记得 Bootstrap 是移动设备优先的吗？） */

/* 小屏幕（平板，大于等于 768px） */
@media (min-width: @screen-sm-min) { ... }

/* 中等屏幕（桌面显示器，大于等于 992px） */
@media (min-width: @screen-md-min) { ... }

/* 大屏幕（大桌面显示器，大于等于 1200px） */
@media (min-width: @screen-lg-min) { ... }
```



通过下表可以详细查看 Bootstrap 的栅格系统是如何在多种屏幕设备上工作的。

|                       | 超小屏幕 手机 (<768px)     | 小屏幕 平板 (≥768px)                                | 中等屏幕 桌面显示器 (≥992px) | 大屏幕 大桌面显示器 (≥1200px) |
| :-------------------- | :------------------------- | :-------------------------------------------------- | :--------------------------- | :---------------------------- |
| 栅格系统行为          | 总是水平排列               | 开始是堆叠在一起的，当大于这些阈值时将变为水平排列C |                              |                               |
| `.container` 最大宽度 | None （自动）              | 750px                                               | 970px                        | 1170px                        |
| 类前缀                | `.col-xs-`                 | `.col-sm-`                                          | `.col-md-`                   | `.col-lg-`                    |
| 列（column）数        | 12                         |                                                     |                              |                               |
| 最大列（column）宽    | 自动                       | ~62px                                               | ~81px                        | ~97px                         |
| 槽（gutter）宽        | 30px （每列左右均有 15px） |                                                     |                              |                               |
| 可嵌套                | 是                         |                                                     |                              |                               |
| 偏移（Offsets）       | 是                         |                                                     |                              |                               |
| 列排序                | 是                         |                                                     |                              |                               |



对应的屏幕大小

| col-xs-数值              |   col-sm-数值     | col-md-数值     |    col-lg-数值   |
| ------------------------------------------------------------ | ---- | ---- | ---- |
| 超小屏（自动）       |     小屏（750px）  |     中屏（970px）   |    大屏（1170px）  |
|                                                              |      |      |      |
| 测试代码： |      |      |      |

1 列组合

​		列总和不能超过12 大于12 则自动到下一行



```html
<!-- 布局容器 -->
    <div class="container">
        <!--     行元素    -->
        <div class="row">
<!--            列元素   col-xs-数值      col-sm-数值    col-md-数值   col-lg-数值
                        超小屏（自动）      小屏（750px）  中屏（970px） 大屏（1170px）
 -->
            <div class="col-md-4 col-sm-4" style="background-color: deepskyblue">4</div>
            <div class="col-md-8 col-sm-8" style="background-color: darkgoldenrod">8</div>
        </div>

        <hr>

        <!-- 列组合 -->
        <div class="row">
            <div class="col-md-1" style="background-color: #0f0f0f">1</div>
            <div class="col-md-1" style="background-color: #1b6d85">1</div>
            <div class="col-md-1" style="background-color: #3e8f3e">1</div>
            <div class="col-md-1" style="background-color: #d43f3a">1</div>
        </div>
        <hr>
        <div class="row">
            <div class="col-md-6" style="background-color: #0f0f0f">1</div>
            <div class="col-md-6" style="background-color: #1b6d85">1</div>
        </div>

        <hr>
        <div class="row">
            <div class="col-md-4" style="background-color: #0f0f0f">1</div>
            <div class="col-md-4" style="background-color: #1b6d85">1</div>
            <div class="col-md-4" style="background-color: #d9edf7">1</div>
        </div>
        <hr>
        <!-- 列不能超过12 不然会到下一行-->
        <div class="row">
            <div class="col-md-4" style="background-color: #0f0f0f">超过12</div>
            <div class="col-md-4" style="background-color: #1b6d85">超过12</div>
            <div class="col-md-4" style="background-color: #d9edf7">超过12</div>
            <div class="col-md-4" style="background-color: #b9def0">超过12</div>
        </div>
    </div>
```

2、列偏移（offset）

​		当前元素增加了左侧的边距（margin）但是列偏移与列组合不能超过12，如果超过就会到下一行

```html
        <!-- 列偏移 -->
        <div class="row">
            <div class="col-md-1" style="background-color: #0f0f0f">1</div>
            <div class="col-md-1 col-md-offset-1" style="background-color: #1b6d85">1</div>
            <div class="col-md-1" style="background-color: #3e8f3e">1</div>
            <div class="col-md-1 col-md-offset-1" style="background-color: #d43f3a">1</div>
        </div>
```

3、列排序

通过使用 `.col-md-push-*` 和 `.col-md-pull-*` 类就可以很容易的改变列（column）的顺序。

```html
        <!-- 列排序 -->
        <div class="row">
            <div class="col-md-1" style="background-color: darkred">红</div>
<!--            往后移动 3个位置-->
            <div class="col-md-1 col-md-push-3" style="background-color: deepskyblue">蓝</div>
            <div class="col-md-1" style="background-color: gray">绿</div>
<!--            往前移动 3个位置-->
            <div class="col-md-1 col-md-pull-3" style="background-color: darksalmon">1</div>
        </div>
```

4、列嵌套

```html
   <div class="row">
                <div class="col-md-6" style="background-color: #d43f3a">
                    <!--  列嵌套 row-->
                    <div class="row">
                        <div class="col-md-2" style="background-color: beige">
                            row 2
                        </div>
                        <div class="col-md-3" style="background-color: red">
                            row 3
                        </div>
                        <div class="col-md-3" style="background-color: gray">
                            row 3
                        </div>
                        <div class="col-md-3" style="background-color: green">
                            row 3
                        </div>
                        <div class="col-md-1" style="background-color: black">
                             1
                        </div>
                    </div>
                </div>
            <div class="col-md-6" style="background-color: darksalmon">
1111
            </div>
        </div>
```

col-sm-* 兼容小屏的 ，下面的代码在 页面排版2列 （小屏）

```html
        <div class="row">
            <div class="col-md-3 col-sm-6" style="background-color: darkred">红</div>
            <!--            往后移动 3个位置-->
            <div class="col-md-3 col-sm-6" style="background-color: deepskyblue">蓝</div>
            <div class="col-md-3 col-sm-6" style="background-color: gray">绿</div>
            <!--            往前移动 3个位置-->
            <div class="col-md-3 col-sm-6" style="background-color: darksalmon">1</div>
        </div>
```

### 常用的样式

1.标题
    bootstrap 对h1-h6的标题的效果的进行覆盖
    提供了对应的类名，为非标题的元素设置样式 .h1-.h6
    副标题 small 标签 或 .small

```html
    <h1>标题1<small>副标题</small></h1>
    <h2>标题2 <div class="small">副标题</div></h2>
    <h3>标题3</h3>
    <div class="h5">您好</div>
```



2.段落
   通过 .lead 来突出强调内容（其作用就是增大文本字号，加粗文本，而且对行高和margin也做了相应的处理）

```html
   <small> 小号
   <b><strong> 加粗
   <i><em> 倾斜
```

```html
<p>通过 .lead 来突出强调内容（其作用就是增大文本字号，加粗文本，而且对行高和margin也做了相应的处理）</p>
    <p class="lead">通过 .lead <i>来</i> 突 <em>出 </em><small> 强调</small>内容（其作用就是增大文本字号，<b>加粗</b>文本，而且对 <strong>行高</strong> 和margin也做了相应的处理）</p>
```



3.强调

```html
  <p class="text-muted">...</p>
  <p class="text-primary">...</p>
  <p class="text-success">...</p>
  <p class="text-info">...</p>
  <p class="text-warning">...</p>
  <p class="text-danger">...</p>

```

```html
    <div class="text-muted">text-muted 提示，使用浅灰色(#999)</div>
    <div class="text-primary">text-primary 主要 使用蓝色 (#428bca)</div>
    <div class="text-success">text-primary 成功 使用浅绿色 (#3c763d)</div>
    <div class="text-info">text-info 提示 使用浅蓝色 (#31708f)</div>
    <div class="text-warning">text-warning 警告 使用黄色 (#8a6d3b)</div>
    <div class="text-danger">text-danger 危险 使用褐色 (#a94442)</div>
```



4.对齐

```html
<p class="text-left">左对齐</p>
<p class="text-center">居中对齐</p>
<p class="text-right">右对齐</p>
<p class="text-justify">两端对齐</p>
<p class="text-nowrap">No wrap text.</p>
```

````html
 <!-- 对齐效果 -->
    <div class="text-left"> 左对齐 Booststrap 通过定义四个类名来控制文本的对齐风格 </div>
    <div class="text-center"> 居中对齐 Booststrap 通过定义四个类名来控制文本的对齐风格 </div>
    <div class="text-right"> 右中对齐 Booststrap 通过定义四个类名来控制文本的对齐风格 </div>

    <p class="text-justify">
      两端对齐 Booststrap 通过定义四个类名来控制文本的对齐风格  Booststrap 通过定义四个类名来控制文本的对齐风格  Booststrap 通过定义四个类名来控制文本的对齐风格  Booststrap 通过定义四个类名来控制文本的对齐风格  Booststrap 通过定义四个类名来控制文本的对齐风格
    </p>
````

### 列表

在HTM文档有三种

​	无序列表 (<ul> <li>....</li></ul>)

​	有序列表  (<ol> <li>....</li></ol>)

​	定义列表 (<dl> <dt>....</dt></dl>)



#### 去点列表：

​	class="list-unstyled"

```html
<ul class="list-unstyled">
    <li>无序列表一</li>
    <li>无序列表二</li>
</ul>
```

#### 内联列表

class="list-inline" 把垂直列表换成水平列表，而且去掉项目符合（编号），保持水平显示，也可以说内联列表就是为了制作水平导航而生

```html
<ul class="list-inline">
  	<li>首页</li>	
  	<li>Java学院</li>	
   <li>在线课堂</li>	
</ul>
```

#### 自定义列表

在原有的基础加入了一些样式，使用样式class="dl-horizontal" 制作水平定义列表；当标题宽度超过160px时，将会显示三个省略号

```html
<dl>
  <dt>HTML</dt>
  <dd>超文本标记语言</dd>
  <dt>CSS</dt>
  <dd> 层叠样式表是一种样式语言 </dd>
</dl>
<dl class="dl-horizontal">
	 <dt>HTML 超文本标记语言</dt>
  <dd>HTML 称为超文本标记语言，是一种标识性的语言</dd>
  <dt>测试标识不能超过160px 的宽度，否则2个点</dt>
  <dd> 层叠样式表是一种样式语言层叠样式表是一种样式语言层叠样式表是一种样式语言 </dd>
</dl>

```

### 代码

使用<code></code>显示单行的代码

```html
<code>this is code</code>
```

使用<kbd></kbd>显示快捷

```html
<p>使用<kbd>ctrl</kbd> + <kbd>s</kbd> 保持内容 </p>
```

使用 <pre></pre>来显示多行代码

```html
<pre>
	public function main(){
			
	}
</pre>
```

注意:

​		1、代码会保留原本的格式，包括空格与回车

​        2、显示html 代码需要使用字符实体

``` html
<pre>
&lt;h2&gt;你好&lt;/h2&gt;
</pre>
```

​    3、当长度超过指定值，可以添加滚动条

```html
<pre class="pre-scrollable">
		<ol>
			<li>..................</li>
			<li>..................</li>
			<li>..................</li>
			<li>..................</li>
			<li>..................</li>
			<li>..................</li>
			<li>..................</li>
			<li>..................</li>
			<li>..................</li>
			<li>..................</li>
			<li>..................</li>
			<li>..................</li>
			<li>..................</li>
		</ol>
</pre>
```

表格：

​	boostrap 为表格提供了1种基础样式和4种附加颜色以及1个支持响应式的表格，在使用boostrap 的表格过程中，只需要添加对应的类名就可以得到不同的表格风格

基础样式：

​		.table: 基础表格

附加样式：

​		.table-striped: 斑马线表格

​		.table-bordered: 带边框的表格

​		.table-hover: 鼠标悬停高亮的表格

​        .table-condensed: 紧凑型表格，单元格没有内距较其他表格的内距小

tr、th、td 样式

通过这些状态类可以为行或单元格设置颜色。

| Class      | 描述                                 |
| :--------- | :----------------------------------- |
| `.active`  | 鼠标悬停在行或单元格上时所设置的颜色 |
| `.success` | 标识成功或积极的动作                 |
| `.info`    | 标识普通的提示信息或动作             |
| `.warning` | 标识警告或需要用户注意               |
| `.danger`  | 标识危险或潜在的带来负面影响的动作   |

```html
<table class="table table-bordered table-striped table-hover table-condensed">
      <tr class="active">
        <td>JavaSe</td>
        <td>数据库</td>
        <td>redis</td>
      </tr>
  <tr class="success">
    <td>JavaSe</td>
    <td>数据库</td>
    <td>redis</td>
  </tr>
  <tr class="warning">
    <td>JavaSe</td>
    <td>数据库</td>
    <td>redis</td>
  </tr>
  <tr class="info">
    <td>JavaSe</td>
    <td>数据库</td>
    <td>redis</td>
  </tr>
  <tr class="danger">
    <td>JavaSe</td>
    <td>数据库</td>
    <td>redis</td>
  </tr>
  <tr>
    <td>JavaSe</td>
    <td>数据库</td>
    <td>redis</td>
  </tr>
  <tr>
    <td>JavaSe</td>
    <td>数据库</td>
    <td>redis</td>
  </tr>
</table>
```

### 表单

1、文本框
      .form-control 表单元素的样式
      表单控制的大小 .input-lg  .input-sm

```html
  <div class="row">
    <div class="col-md-3">
      <input type="text" class="form-control" /> <br>
        <input type="text" class="form-control input-lg" /> <br>
        <input type="text" class="form-control input-sm" /> <br>
    </div>
  </div>
```



   2、下拉框
      .form-control 表单元素的样式
      multiple="multiple" 设置下拉多选

```html
  <div class="row">
    <div class="col-md-3">
<!--    单选 -->
      <select name="" id="select" class="form-control">
        <option value="0">请选择</option>
        <option value="1">上海</option>
        <option value="2">北京</option>
        <option value="3">南京</option>
      </select>
<!--   多选择-->
      <select name="" id="multiple" class="form-control" multiple="multiple">
        <option value="0">请选择</option>
        <option value="1">上海</option>
        <option value="2">北京</option>
        <option value="3">南京</option>
      </select>
    </div>
</div>
```

   3、文本域
      .form-control 表单元素的样式

```html
  <div class="row">
    <div class="col-md-3">
      <textarea name="" class="form-control"></textarea>
    </div>
  </div>

```



   4、复选框
      垂直显示  .checkbox
      水平显示  .checkbox-inline

```html
  <!--    垂直显示-->
  <div class="row">
    <div class="col-md-3">
      <div class="checkbox">
        <label ><input type="checkbox" name="hobby"/>唱歌</label>
      </div>
      <div class="checkbox">
        <label><input type="checkbox" name="hobby"/>跳舞</label>
      </div>
    </div>
  </div>

```

```html
<!--  水平显示-->
  <div class="row">
    <div class="col-md-3">
      <label class="checkbox-inline">
        <input type="checkbox" name="hobby"/>唱歌
      </label>
      <label class="checkbox-inline">
        <input type="checkbox" name="hobby"/>跳舞
      </label>
    </div>
  </div>
```



   5、单选框
      垂直显示  .radio
      水平显示  .radio-inline

```html
  <!--    垂直显示-->
  <div class="row">
    <div class="col-md-3">
      <div class="checkbox">
        <label ><input type="radio" name="sex"/>男</label>
      </div>
      <div class="checkbox">
        <label><input type="radio" name="sex"/>女</label>
      </div>
    </div>
  </div>

```

```html
  <!--  水平显示-->
  <div class="row">
    <div class="col-md-3">
      <label class="radio-inline">
        <input type="radio" name="hobby"/>唱歌
      </label>
      <label class="radio-inline">
        <input type="radio" name="hobby"/>跳舞
      </label>
    </div>
  </div>
```



   6、按钮
     1、使用按钮
          基础按钮 .btn
          附加样式：.btn-default 、.btn-primary 、btn-success 、btn-info 、btn-warning 、btn-danger 、btn-link

```html
<!--  按钮-->
    <button class="btn btn-success">按钮</button>
    <button class="btn btn-danger">按钮</button>
    <button class="btn btn-link">按钮</button>
    <button class="btn btn-info">按钮</button>
    <button class="btn btn-warning">按钮</button>
    <button class="btn btn-default">按钮</button>
<hr>
```



​      2、多标签使用
​          其他标签可以通过按钮样式设置成功按钮效果（a 标签、small 标签 、 div 标签）

```html
<!--通过按钮的样式来设置其他元素为按钮效果-->
  <a href="#" class="btn btn-warning">a 标签</a>
  <span class="btn btn-warning">span 标签</span>
  <div class="btn btn-info">div 标签</div>
  <hr>
```

3、按钮大小
         大按钮： .btn-lg
         小按钮： .btn-xs
         正常按钮  .btn-sm

```h
<!--设置按钮的大小-->
  <button class="btn btn-danger">按钮</button>
  <button class="btn btn-danger btn-lg">按钮</button>
  <button class="btn btn-info btn-sm">按钮</button>
  <button class="btn btn-info btn-xs">按钮</button>
  <hr>
```



4、按钮禁用
       1.通过 disabled 属性（真正的禁用）
       2.通过 disabled 样式 （样式上禁用）

```html
<!--在标签中添加disable 属性-->
  <button class="btn btn-danger" onclick="alert('hello')" disabled="disabled">按钮</button>
  <!--在标签中添加类名"disable" -->
  <button class="btn btn-danger disabled" onclick="alert('hello')">按钮</button>
```

### 表单布局

基本的表单结构是 Boostrap 自带的，个别的表单控制件自动接收一些全局样式，下面列出了创建基本表的步骤

1.向父 <form> 元素添加 role="form"

2.把标签和控制件放在一个带有class.form-group 的 <div> 中, 这是获取最佳距所必须的

3.向所有的文本元素<input>、<textarea> 和 <select> 添加 class="form-control"

#### 水平表单：

同一行显示  form-horizontal

配合 Bootstrap 框架的网格系统

```html
<form action="" class="form-horizontal">
    <h2 align="center">用户信息</h2>
<!--      表单中的元素组-->
    <div class="form-group">
      <label for="uname" class="control-label col-md-2">姓名</label>
      <div class="col-md-8">
        <input type="text" id="uname" class="form-control" placeholder="请输入姓名">
      </div>
    </div>
    <div class="form-group">
      <label for="upwd" class="control-label col-md-2">密码</label>
      <div class="col-md-8">
        <input type="text" id="upwd" class="form-control" placeholder="请输入密码">
      </div>
    </div>
    <div class="form-group">
      <label class="control-label col-md-2">爱好</label>
      <div class="col-md-8 col-md-offset-2">
        <label class="checkbox-inline">
          <input type="checkbox" name="hobby"/>唱歌
        </label>
        <label class="checkbox-inline">
          <input type="checkbox" name="hobby"/>跳舞
        </label>
      </div>
    </div>

    <div class="form-group">
      <label class="control-label col-md-2">城市</label>
      <div class="col-md-8">
        <select name=""  class="form-control">
          <option value="0">请选择</option>
          <option value="1">上海</option>
          <option value="2">北京</option>
          <option value="3">南京</option>
        </select>
      </div>
    </div>

    <div class="form-group">
      <label for="remark" class="control-label col-md-2">简介</label>
      <div class="col-md-8">
        <textarea name="" id="remark" class="form-control"></textarea>
      </div>
    </div>

    <div class="form-group">
      <div class="col-md-2 col-md-offset-6">
       <button class="btn btn-primary">提交</button>
      </div>
    </div>
  </form>
```

#### 内联表单：

同一行显示  form-inline

```html
<form class="form-inline">
    <div class="form-group">
      <label for="username">姓名</label>
      <input type="text" id="username" class="form-control" placeholder="请输入名字">
    </div>
  <div class="form-group">
    <label for="password">密码</label>
    <input type="text" id="password" class="form-control" placeholder="请输入密码">
  </div>
  <div class="form-group">
    <button class="btn btn-primary">提交</button>
  </div>
</form>
```

### 缩略图

缩略图在电商类的网站很常见，最常见的地方就是产品的列表页面，缩略图的实现是配合网格系统一起使用，同时还可以让缩略图配合标题，描述内容，按钮等。

```html
 <div class="container">
      <div class="row">
          <div class="col-md-3">
            <div class="thumbnail">
                <img src="img/zly.jpeg" style="width: 240px;height: 360px">
                <h3>赵丽颖</h3>
                <p>出生什么神 演员</p>
                <button class="btn btn-default">
                    <span class="glyphicon glyphicon-heart"> 喜欢</span>
                </button>
                <button class="btn btn-info">
                    <span class="glyphicon glyphicon-pencil"> 评论</span>
                </button>
            </div>
          </div>
        <div class="col-md-3">
          <div class="thumbnail">
            <img src="img/qw.jpeg" style="width: 240px;height: 360px">
            <h3>戚薇</h3>
            <p>出生什么神 演员</p>
            <button class="btn btn-default">
              <span class="glyphicon glyphicon-heart"> 喜欢</span>
            </button>
            <button class="btn btn-info">
              <span class="glyphicon glyphicon-pencil"> 评论</span>
            </button>
          </div>
        </div>
        <div class="col-md-3">
          <div class="thumbnail">
            <img src="img/lyf.jpeg" style="width: 240px;height: 360px">
            <h3>刘亦菲</h3>
            <p>出生什么神 演员</p>
            <button class="btn btn-default">
              <span class="glyphicon glyphicon-heart"> 喜欢</span>
            </button>
            <button class="btn btn-info">
              <span class="glyphicon glyphicon-pencil"> 评论</span>
            </button>
          </div>
        </div>
        <div class="col-md-3">
          <div class="thumbnail">
            <img src="img/rs.jpeg" style="width: 240px;height: 360px">
            <h3>认识</h3>
            <p>出生什么神 演员</p>
            <button class="btn btn-default">
              <span class="glyphicon glyphicon-heart"> 喜欢</span>
            </button>
            <button class="btn btn-info">
              <span class="glyphicon glyphicon-pencil"> 评论</span>
            </button>
          </div>
        </div>
      </div>
  </div>
```

面板 

​	默认的.panel 组件所做的只是设置基本的边框(border) 和内补(padding) 来包含内容

.panel-default: 默认样式

.panel-heading: 面板头

.panel-body: 面板主体内容

```html
  <div class="panel panel-warning">
    <div class="panel-heading">
        .....
    </div>
    <div class="panel-body">
      eqwe
    </div>
```

代码

```html
 <div class="panel panel-warning">
    <div class="panel-heading">
       <h2 align="center">明星合集</h2>
    </div>
    <div class="panel-body">
        <div class="row">
            <div class="col-md-3">
                <div class="thumbnail">
                    <img src="img/zly.jpeg" style="width: 240px;height: 360px">
                    <h3>赵丽颖</h3>
                    <p>出生什么神 演员</p>
                    <button class="btn btn-default">
                        <span class="glyphicon glyphicon-heart"> 喜欢</span>
                    </button>
                    <button class="btn btn-info">
                        <span class="glyphicon glyphicon-pencil"> 评论</span>
                    </button>
                </div>
            </div>
            <div class="col-md-3">
                <div class="thumbnail">
                    <img src="img/qw.jpeg" style="width: 240px;height: 360px">
                    <h3>戚薇</h3>
                    <p>出生什么神 演员</p>
                    <button class="btn btn-default">
                        <span class="glyphicon glyphicon-heart"> 喜欢</span>
                    </button>
                    <button class="btn btn-info">
                        <span class="glyphicon glyphicon-pencil"> 评论</span>
                    </button>
                </div>
            </div>
            <div class="col-md-3">
                <div class="thumbnail">
                    <img src="img/lyf.jpeg" style="width: 240px;height: 360px">
                    <h3>刘亦菲</h3>
                    <p>出生什么神 演员</p>
                    <button class="btn btn-default">
                        <span class="glyphicon glyphicon-heart"> 喜欢</span>
                    </button>
                    <button class="btn btn-info">
                        <span class="glyphicon glyphicon-pencil"> 评论</span>
                    </button>
                </div>
            </div>
            <div class="col-md-3">
                <div class="thumbnail">
                    <img src="img/rs.jpeg" style="width: 240px;height: 360px">
                    <h3>认识</h3>
                    <p>出生什么神 演员</p>
                    <button class="btn btn-default">
                        <span class="glyphicon glyphicon-heart"> 喜欢</span>
                    </button>
                    <button class="btn btn-info">
                        <span class="glyphicon glyphicon-pencil"> 评论</span>
                    </button>
                </div>
            </div>
        </div>
    </div>
  </div>
```

导航：

使用下拉与按钮组件合可以制作导航

要点：

​	1、基本样式： .nav 与  "nav-tabs"、"nav-pills" 组合制作导航

​	2、分类：

​			标签形 ( nav-tabs) 导航

​            胶囊形 ( nav-pills) 导航

​			堆栈 (nav-stacked) 导航

​			自适应( nav-justified)导航

​			面包屑式(breadcumb) , 单独使用的样式，不与nav 一起使用，直接加入到 ol、ul 中即可，一般用于导航，主要是起到作用是告诉用户所在的页面位置（当前页面）

3、状态

​	1 、选中状态： action 样式

​	2、禁止状态 ：disable

#### 标签式导航菜单

```html
<p>标签式导航菜单</p>
<ul class="nav nav-tabs">
    <li class="active"><a href="#">home</a></li>
    <li><a href="#">svn</a></li>
    <li><a href="#">git</a></li>
    <li><a href="#">ios</a></li>
    <li><a href="#">php</a></li>
</ul>
```

#### 胶囊式导航

```html
    <p>胶囊式导航菜单</p>
    <ul class="nav nav-pills">
        <li class="active"><a href="#">home</a></li>
        <li><a href="#">svn</a></li>
        <li><a href="#">git</a></li>
        <li><a href="#">ios</a></li>
        <li><a href="#">php</a></li>
    </ul>
```

#### 面包屑式导航菜单

```html
<p>面包屑式导航菜单</p>
<ul class="breadcrumb">
    <li><a href="#">Home</a></li>
    <li><a href="#">2013</a></li>
    <li class="active">十一月</li>
</ul>
```

#### 分页导航

分页随处可见，分为页面导航和翻页导航

页码导航： ul标签上加上  pagination | pagination-lg | pagination-sm

翻页导航：ul标签加上 pager

分页导航

```html
    <p>分页导航</p>
    <ul class="pagination">
            <li><a href="#">&laquo;</a></li>
            <li><a href="#">1</a></li>
            <li><a href="#">2</a></li>
            <li><a href="#">3</a></li>
            <li><a href="#">4</a></li>
            <li><a href="#">5</a></li>
            <li><a href="#">&raquo;</a></li>
    </ul>
```

#### 模态框

模态框(model) 是覆盖在父窗体上的子窗体，通常，目的是显示来自一个单独的源的内容，可以在不离开父窗体的情况下 有一些互动，子窗体可以提供信息与交互等

两种用法:

​	1 通过 data 属性：在控制器元素（比如按钮或链接）上设置属性 data-toggle= "modal", 同时设置data-target="#identifiler"  或 herf="#identifiler" 来指定要切换的特定的模态框（带有 id="identifler"）

  2 通过JavaScript：使用这种技术，可以通过JavaScript来调用带 id="identifler"的模态框

​     打开模态框：$('#identifier').modal('show')

​     关闭模态框：$('#identifier').modal('hide')

代码：

```html
<h2>创建模态框（Modal）</h2>
<!-- 按钮触发模态框 -->
<button class="btn btn-primary btn-lg" data-toggle="modal" data-target="#myModal">打开模态框</button>
<button class="btn btn-primary btn-lg" id="btn" >打开模态框</button>
<!-- 模态框（Modal） -->
<div class="modal fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="true">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">&times;</button>
        <h4 class="modal-title" id="myModalLabel">模态框（Modal）标题</h4>
      </div>
      <div class="modal-body">在这里添加一些文本</div>
      <div class="modal-footer">
        <button type="button" class="btn btn-default" data-dismiss="modal">关闭</button>
        <button type="button" class="btn btn-primary" id="submit_btn">提交更改</button>
      </div>
    </div><!-- /.modal-content -->
  </div><!-- /.modal -->
</div>
<script type="text/javascript">
      $("#btn").click(function (){
          // 手动打开模态框
          $("#myModal").modal("show")
      });

      $("#submit_btn").click(function (){
        // 手动打开模态框
        $("#myModal").modal("hide")
      });
</script>
```

