#### day01

##### １．文件和文件夹都用File表示
​	１．根据绝对路径来创建一个Fiel实例（注意如果是linux系统的话那么根目录从home开始）
​    	例如        File file1=new File("/home/leiliu/IdeaProjects/file");
​    ２．根据相对路径来创建一个File实例（相对路径是相对于这个项目来说，从src开始）
​    	例如        File file2=new File("src/base/how2j/io/b.txt");
​	３．比较使用的构造方法是
​    	File(File parent, String child)
​		从父抽象路径名和子路径名字符串创建新的 File实例
##### ２．File下比较常用的方法
​	注：文件必须要存在才能使用才能获取长度属性和时间属性
​	１．判断当前文件是否存在　exists
​    ２．判断文件是否是文件夹　isDirectory
​    ３．判断是否是文件	isFile
​    ４．获取文件的长度　length
​    ５．获取文件的最后修改时间　lastModified
​    ６．重命名　renameto(File test)
##### 3.小案例
​	遍历/home的所有文件，并打印出最大长度和最小长度的文件
​    

#### day02

##### １．流的概述
​	流就是一系列数据
​	当不同的介质之间有数据交互的时候，Java就是用流来实现。数据源可以是文件，还可以是数据库，网络甚至是其他的程序。
​    比如读取文件到程序中，站在程序的角度来看，就是输入流。
​    输入流：InputStream
​    输出流：OutputStream
##### ２．文件输入流
​	//通过这个输入流,就可以把数据从硬盘,读取到Java的虚拟中来
​    FileInputStream fileInputStream = new FileInputStream(file);
##### ３．文件输出流
​	

```java
File file=new File("src/base/how2j/io/b.txt");
​    System.out.println(file.getAbsolutePath());
​        try {
​        	//从程序中读取到硬盘中
​            FileOutputStream fileOutputStream=new FileOutputStream(file);
​            System.out.println(fileOutputStream);
​        } catch (FileNotFoundException e) {
​            e.printStackTrace();
​        }
```

#### day03

##### 字节流
​	Ａscii码　所有存放在计算机中都是以属性形式存放的，所以字母需要转换为数字才能够存放。ascii码表，只包含简单的英文字母，符号数字等等。
​	输入流
​    	FileInputStream
​    输出流
​    	FileOutputStream
​    此外可以多学习一下File的方法，例如file.mkdirs()来创建目录

##### 文件的拆分和合并
​	**拆分**
​    	如何进行拆分
​        	1.将文件读入到内存中
​            2.创建byte[],一开始使用定长，后面发现最大长度完全够用
​            3.我遇到的问题是如何保证每个子文件读取定长byte
​            解决方案：定义count值，表示要分成多少个子文件，然后利用write(byte[] byte,int off,int len)进行操作。

```java
            for (int i = 1; i <= count; i++) {
                if(i<count){
                    FileOutputStream fileOutputStream=new FileOutputStream("src/base/how2j/io/xyz/"+filename+"-"+(i-1));
                    fileOutputStream.write(bytes,(i-1)*childLen,childLen);
                    fileOutputStream.close();
                }else{
                    FileOutputStream fileOutputStream=new FileOutputStream("src/base/how2j/io/xyz/"+filename+"-"+(i-1));
                    fileOutputStream.write(bytes,(i-1)*childLen,fileLen-(i-1)*childLen);
                    fileOutputStream.close();
                }
            }
```

  **合并**
    	如何进行合并
        	1.一开始想的是放到一个数组里再写到文件里，后面看到别人思路后发现没这么麻烦，
            2.思路：new FileOutputStream(filepath + "/" + filename, true)这里可以追加到文件里我们只需要循环读取。

```java
            	File[] files=file.listFiles();
        for (int i = 0; i < files.length; i++) {
            try {
                File childFile = new File(filepath + "/" + filename + "-" + i);
                FileInputStream fileInputStream = new FileInputStream(childFile);
                byte[] bytes = new byte[(int) childFile.length()];
                fileInputStream.read(bytes);
                //这里参数true表示追加
                FileOutputStream fileOutputStream = new FileOutputStream(filepath + "/" + filename, true);
                fileInputStream.close();
                fileOutputStream.close();
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
```

#### day04

##### 标准流的关闭方式
在finally中关闭流
１．首先把流的引用声明在try的外面，如果声明在try里面，其作用域无法抵达finally.
２．在finally关闭之前，要先判断该引用是否为空.
３．关闭的时候，需要再一次进行try catch处理.

```java
File file = new File("src/base/how2j/io/b.txt");
FileInputStream fileInputStream=null;
try {
    fileInputStream =new FileInputStream(file);
    byte[] all=new byte[(int)file.length()];
    fileInputStream.read(all);
    for(byte b:all){
        System.out.println(b);
    }
} catch (FileNotFoundException e) {
    e.printStackTrace();
} catch (IOException e) {
    e.printStackTrace();
}finally {
    //在finally里关闭流
    if(null!=fileInputStream){
        try {
            fileInputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
在try中关闭
	原理是因为所有的流的类都实现了AutoClosea le，任何实现了这个接口，都可以在try()中进行实例化。并且try,catch，finally结束的时候自动关闭，回收相关资源。

```java
File f=new File("src/...");
try(FileInputStream fis=new FileInputStream(f)){
...
}
```

#### day05

##### 字符流
​	Reader字符输入流
​    Writer字符输出流
​    专门用于字符形式的读取和写入数据
​    特别是从文件读取的特殊字符例如中文字符和特殊符号，这也是和字节流的一个重要区别
##### 字符加密
​	学习心得：
​    	1.api Character.isDigit()和Character.isUpperCase()和isLowCase()方法
​    	2.读取中文的原因会多出几个空格，要想办法把空格处理掉
​ 

```java
 if (Character.isDigit(code[i])) {
​                    if (code[i] == '9') {
​                        code[i] = '0';
​                        continue;
​                    }
​                    code[i]++;
​                } else if (Character.isUpperCase(code[i]) || Character.isLowerCase(code[i])) {
​                    if (code[i] == 'Z' || code[i] == 'z') {
​                        code[i] -= 26;
​                    }
​                    code[i]++;
​                }
​                int len = fileReader.read(code);
```

