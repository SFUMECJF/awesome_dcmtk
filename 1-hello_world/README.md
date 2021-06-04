开发环境：
<p align="center">
  <a href="https://github.com/vuejs/vue">
    <img src="https://img.shields.io/badge/Windows-10-brightgreen.svg" alt="ubuntu">
  </a>
  <a href="https://github.com/vuejs/vue-router">
    <img src="https://img.shields.io/badge/VisualStudio-2017--2019-brightred.svg" alt="vue-router">
  </a>
  <a href="https://github.com/vuejs/vuex">
    <img src="https://img.shields.io/badge/CMake-3.14.3-brightgreen.svg" alt="vuex">
  </a>
</p>  
[toc]

## 0  前言
使用`c++`做医学图像的处理任务，离不开`dcmtk`这个库，包括`itk-snap`在内的众多开源软件都使用该库进行开发，如果你还为使用什么库而纠结，建议直接使用dcmtk开整，肯定不会让你失望的。
###  为什么使用visual  studio？
首先我们下载的是dcmtk库的源代码，需要在我们自己的机器 上将dcmtk 编译为我们所需要的动态库，因此需要visual studio做这个工作。当编译生成好了我们所需要的dcmtk头文件以及库文件之后，其实可以使用其他的ide比如`qt`、`Clion`来进行下一步的开发，不过在本文安装好环境之后，仍然使用visual studio来进行代码验证。
PS: vs2017以及vs2019均可进行下载。
###  如何安装？
bilibili已经有很好 的视频教程了。不过视频教程有一些瑕疵，建议读者打开该视频，一步步操作，同时，在每一步的操作过程中，查看本文所描述的操作成功后会显示的结果，决定是否进行下一步，或者在本步骤debug。
[C++-CMake-Dcmtk视频环境配置教程](https://www.bilibili.com/video/BV1m4411T728?from=search&seid=5054790125566476902)
###  其他一些可参考资料，实在觉得本文无法满足需求，可以查看
[视频作者的英文版文字教程](https://www.programmersought.com/article/9609749977/)

[视频作者的中文版文字教程](https://www.jianshu.com/p/b06349d609ba)

[不错的vs2017环境配置教程](https://blog.csdn.net/oYangLi1/article/details/78904175)

[官方的安装教程，我猜你点开就不想看了，相信我](https://support.dcmtk.org/docs/file_install.html)

Tips：1.在看视频过程中，先整体浏览一遍，有一些视频讲错了，回退了一下操作。这些地方要注意。2.视频版不好回退，建议使用文字版

##  1  下载dcmtk源代码

[dcmtk下载链接](https://dicom.offis.de/dcmtk.php.en)
![在这里插入图片描述](https://gitee.com/umecjf/figures/raw/master/20210603111336316.png)
![在这里插入图片描述](https://gitee.com/umecjf/figures/raw/master/20210603111336316.png)

我最终下载的支持库文件名字为[dcmtk-3.6.6-win64-support-MD-iconv-msvc-15.8.zip](https://dicom.offis.de/download/dcmtk/dcmtk366/support/dcmtk-3.6.6-win64-support-MD-iconv-msvc-15.8.zip)

###  1.2  下载文件时不同参数的比较
首先选择`MD`或者`MDd`的，因为动态库是潮流，mt是构建静态库，代码会很大。
关于`字符集`，选择icu64是很庞大繁琐的库，libiconv可以轻松用于windows。


[字符集比较](https://stackoverflow.com/questions/7372328/icu-c-converting-encodings)

[字符集使用范围比较](https://cpp.libhunt.com/compare-ibm-icu-vs-libiconv)

[dcmtk在windows安装，开篇提到了md和mt的区别](https://www.programmersought.com/article/9609749977/)



## 2  CMake构建
注意：`CMake版本一定一定要小于3.19.0`，建议选择3.14.3，如果你的版本很新，去卸载重装！不会超过15分钟的。
[3.14下载链接](https://github.com/Kitware/CMake/releases?after=v3.14.5)

点两次configure，一次generate。
###  2.1  configure(10分钟)
跟随视频教程


####  可能的错误（没有可不看）

```cmake
CMake Deprecation Warning at CMakeLists.txt:2 (cmake_minimum_required):
  Compatibility with CMake < 2.8.12 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.

```
在`cmakelists.txt`中把最低要求的CMake版本修改了一下，没有红色错误了。
可能是代码中设置的最低版本是2.8.12，太旧了。只是一个警告，不影响运行。具体内容看：https://cmake.org/cmake/help/latest/release/3.19.html#deprecated-and-removed-features
![在这里插入图片描述](https://gitee.com/umecjf/figures/raw/master/20210603111336316.png)

###  2.2  第二次configure
结果如下：
![在这里插入图片描述](https://gitee.com/umecjf/figures/raw/master/20210603111336316.png)
其中写的很多标准库用不了，正常现象，这是dcmtk提供的。我们不用它的库，使用编译器自带的STL即可。

###  2.3  generate
跟随视频教程
####  可能的错误（没有就不用看）
```shell
CMake Warning (dev) at CMake/dcmtkMacros.cmake:82 (add_library):
  Policy CMP0115 is not set: Source file extensions must be explicit.  Run
  "cmake --help-policy CMP0115" for policy details.  Use the cmake_policy
  command to set the policy and suppress this warning.

  File:

    D:/Programs/dcmtk-3.6.6/ofstd/libsrc/ofchrenc.cc
Call Stack (most recent call first):
  ofstd/libsrc/CMakeLists.txt:2 (DCMTK_ADD_LIBRARY)
This warning is for project developers.  Use -Wno-dev to suppress it.

```
`cmake_policy(SET CMP0115 OLD)`没有用。
使用下载3.20版本，安装3.14版本，成功解决问题。！15分钟内解决。

## 3  VS生成dcmtk的头文件和库

生成过程很长，半小时以上。我的台式机都用了很长时间。

总共有97（不同版本不一样，图片显示204）个项目，要编译链接97次，任何一次错了都不行。正确结果如下（`如果之前CMake中出现红色错误，这一步肯定不会成功！`）：
![在这里插入图片描述](https://gitee.com/umecjf/figures/raw/master/20210603111336316.png)

####  可能的错误
都是一模一样的错误，是因为使用了较新的CMake版本的原因。CMake不应该出现任何一行红色。
```shell
严重性	代码	说明	项目	文件	行	禁止显示状态

错误	C1083	无法打开包括文件: “unicode/ucnv.h”: No such file or directory	ofstd	D:\Programs\dcmtk-3.6.6\ofstd\libsrc\ofchrenc.cc	67	

错误	LNK1104	无法打开文件“..\..\lib\Debug\ofstd.lib”	oflog	D:\Programs\dcmtksolution\oflog\libsrc\LINK	1	

```



## 4  验证安装效果
使用vs打开工程，进行一些配置，如下是省得手打的部分配置，可以直接复制进去。
```shell
iphlpapi.lib
ws2_32.lib
wsock32.lib
netapi32.lib
ofstd.lib
oflog.lib
dcmdata.lib
zlib_d.lib
```
注意：`当你给自己的代码工程配置库文件以及头文件路径的时候，一定要注意是debug还是release文件，如果配置错了，你就会看到找不到dcmtk的头文件的错误！`也就是下面那里要注意：
![在这里插入图片描述](https://gitee.com/umecjf/figures/raw/master/20210603111336316.png)

###  代码下载
如果你是vs2019,可以直接点击github链接（里面包含了需要的dicom文件以及代码），下载并双击sln文件，注意要把我工程中的库文件和头文件目录修改为你的！

运行结果如下则正确！
![在这里插入图片描述](https://gitee.com/umecjf/figures/raw/master/20210603153445603.png)

###  开源的dicom文件下载
[左下角dicom samples导出dicom文件](https://www.dicomlibrary.com/)

## 5  教程
本人正在编写dicom教程，欢迎同好一起交流。强烈建议一起参与[github仓库](https://github.com/SFUMECJF/awesome_dcmtk)。



[2010年的远古教程](https://dicomiseasy.blogspot.com/p/introduction-to-dicom.html)

或者你也可以关注我的微信公众号：三丰杂货铺

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200529103009878.gif#pic_center#pic_center)