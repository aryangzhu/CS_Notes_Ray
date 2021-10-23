##### HTTML+CSS+JavaSrcipt即 页面结构+样式+脚本
###### １．什么是css
​	1.概述
​    casscading sytle sheet　层叠式级联样式表
​    css:表现(美化网页)
​    字体，颜色，边距，高度，宽度，背景图片，网页定位，网页浮动...
###### ２．发展史
​    css1.0
​    css2.0 div(块)＋css,html与css结构分离的思想，网页变得简单，seo
​    css2.1 浮动，定位
​    css3.0　圆角，阴影，动画...浏览器兼容性

###### ３．快速入门
​        <!--规范，<style>可以编写css的代码,没一个生命,最好使用分号结尾-->
​        语法：
​        	选择器｛
​            	声明１;
​                声明２;
​                声明３;
​           	｝
​	当然也可以文件夹的形式将css文件保存在里面，在head头部中时候用<link>标签进行引入，
​    <link rel="stylesheet" 	href="css/style.css"/>
​    目录结构
​    lesson01
​    	1.我的一地个css程序
​        	css
​            	style.css
​            index.html

    ###### 4.css的优势
​    	１．内容和表现分离
​        ２．网页结构表现统一，可以实现复用
​        ３．样式十分的丰富
​        ４．建议使用独立于html的css文件
​        ５．利用SEO（搜索引擎优化，它是一种通过分析搜索引擎的排名规律，了解各种搜索引擎怎样进行搜索、怎样抓取互联网页面、怎样确定特定关键词的搜索结果排名的技术），容易被搜索引擎收录。
 ###### ５．３种导入方式
​    	展示优先级别：就近原则－－谁离这个标签越近谁就起作用
​    	１.行内样式
​        	<!--行内样式：在标签中元素中,编写过一个style属性,编写样式即可-->

			<h1 style="color: red">我是标题</h1>
​        ２．内部样式
        	<style>
        		h1{
        		    color:green;
        		}
    		</style>
​            

        ３．外部样式
        	链接式（推荐使用）
        	    <link rel="stylesheet" href="css/style.css"/>
    		导入式
            	使用的比较少
            	<style>
            		@import url("css/style.css");
            	</style>