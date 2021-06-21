# maven编译 Process terminated【已解决】

▐ maven项目编译报错如下：

![在这里插入图片描述](01-Maven-Q.assets/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDIwMzE1OA==,size_16,color_FFFFFF,t_70.png)

▐ 点击【项目名】提示找到出错文件

![在这里插入图片描述](01-Maven-Q.assets/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDIwMzE1OA==,size_16,color_FFFFFF,t_70-20210522213816593.png)

▐ 点击查看出错文件
在idea中打开了settings文件，找到提示的报错位置

![在这里插入图片描述](01-Maven-Q.assets/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDIwMzE1OA==,size_16,color_FFFFFF,t_70-20210522213825112.png)

▐ 原因及解决办法
原因 ：缩进或者空格不一致导致该问题
解决办法：格式化编辑好之后复制再粘贴过来就可以了(推荐 👉XML 在线格式化)
------------------------------------------------