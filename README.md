# The_Adb_Cmd
ADB 一文搞定
## 一、什么是adb

adb的全称为Android Debug Bridge,安卓调试桥,属于是安卓设备和电脑之间的中间件,是官方提供的用于操作安卓设备的工具。  <br>
adb的使用场景不仅仅局限于安卓手机,安卓平板、部分游戏手柄、安卓vr等安卓设备均可使用。

## 二、adb用来干什么?

在PC终端进入安卓设备shell,通过命令行操作安卓设备;  <br>
pc和安卓安卓设备之间传输文件;  <br>
打开、关闭安卓设备或其应用;  <br>
模拟点击、输入、滑动等操作;  <br>
输出设备信息、性能指标信息  

## 三、adb工作原理:

采用客户端-服务器模式(C/S模式),分为:  <br>
PC:  <br>
客户端:client  <br>
服务器:server  <br>
MACHINE:(安卓设备)  <br>
守护进程:daemon  <br>
流程:服务器监听5037端口,接受客户端发来的指令发送给移动设备端adbd,5555端口和移动端建立连接,5554则是和控制台建连接。  <br>

## 四、adb:

#最新版本的ADB下载(包含多系统版本):  https://adbshell.com/  <br>
 linux也可以在线安装:sudo apt-get install android-tools-adb  <br>
           离线安装：参考 https://www.jianshu.com/p/59799fa142e9   <br>

#windows配置ADB环境:  <br>
1.1：打开控制面板 >系统和安全>系统>高级系统设置  <br>
1.2:在系统变量中新建ANDROID_HOME变量，赋值路径(platform-tools的上一级目录例如：C:\Users\SweetOrange\AppData\Local\Android\Sdk\)  <br>
1.3.在系统变量path中添加%ANDROID_HOME%\platform-tools  <br>
1.4.cmd进入终端  <br>
可以参考https://blog.csdn.net/kasumi8874/article/details/123756858  <br>


## 五、连接移动设备以及查看设备连接状态
本文以安卓手机真机为例说明,下同。  <br>
adb真机调试使用前提:  <br>
// 真机插入USB数据线打开调试模式,PC端安装手机驱动,弹出是否允许调试点击是<br>  
// adb调试手机需要打开手机开发者模式、USB调试 弹出的USB配置窗口建议选择文件传输   <br>
// 特殊命令还需要打开其他开关,例如无线调试的时候需要提前打开无线调试开关  <br>

检查 adb 是否安装成功:  <br>
1、adb shell 是否进入安卓设备shell  <br>
   adb shell 在一切正常情况下可以进入到安卓设备的内环境使用linux命令进行操作(在linux环境下)  <br>
2、adb --version 是否有adb 版本显示  <br>
1和2有一个成功就说明安装成功  <br>

获取设备序列号:adb shell getprop ro.serialno  <br>
               adb get-serialno  <br>
               adb  devices  <br>
               adb devices -l   //获取更加详细的设备信息,含序列号、型号等  <br>
获取型号:adb -d shell getprop ro.product.model  <br>
获取厂商:adb -d shell getprop ro.product.brand  <br>

获取连接状态:adb get-status     <br>
         结果: device: 设备正常连接 <br> 
               offline:连接出现异常,设备无响应  <br>
               error: no devices/emulators found、unknown:没有连接设备   <br>
获取运营商:adb shell getprop gsm.sim.operator.alpha  <br>

无线调试连接方法:  <br>
最终实现无线连接必须以安卓设备和PC已经连接同一个无线网为前提。  <br>
#1、借助USB数据线(常用机型任意安卓版本):  <br>
   先连接USB数据线,执然后行adb tcpip [设置一个端口号],在执行adb connect [手机ip]:[端口号]即可  <br>
   adb tcpip [设置一个端口号] 是在手机上开辟一个新的端口,用于监听,  <br>
   然后当PC端执行adb connect [手机ip]:[端口号]时,将会通过设置的连接手机端口和无线网进行连接；  <br>
#2、无需USB数据线(安卓11及以上):  <br>
   adb pair [IP]:[port]  <br>
   这里的端口和IP都是在手机上显示的,需要在安卓手机设置中打开无线调试  <br>

从无线模式重新连接到USB命令:adb usb  <br>

## 六、adb命令格式:
 
adb [-d | -e | -s <serialNumber>] <command>  <br>
一台设备不需要以下命令,默认会操作已连接的设备:  <br>
         -d 指定当前唯一通过USB连接安卓设备为命令目标  <br>
         -e 指定当前唯一运行的模拟器为命令目标  <br>
         -s  [device名字 ]指定相应的设备为命令目标  <br>
以上[ ] 内为可选,如果只有一个模拟器在运行或者只连接了一个设备,系统会默认将 adb 命令发送至该设备  <br>

Ps:   adb shell [其他命令]  <br>
直接执行这样的长命令 == 先执行 adb shell,再执行括号内的其他命令;这两个效果是一样的  <br>
Eg:adb shell ls -a == 先执行adb shell ,再执行ls -a  <br>
 
## 七、安装应用:  

安装包在PC端:  <br>
普通安装: adb  install     <apk路径>    //原来安卓设备中没有安装该应用  <br>
覆盖安装: adb  install -r  <apk路径>    //安卓设备中已经安装该应用,使用这个安装包覆盖之前版本  <br>
授权安装: adb  install -g  <apk路径>    //授予安装软件运行中的所有权限,不必在软件运行用到权限时一一来授权所需权限  <br>
安装成功会返回成功提示 “Success”;  <br>
 
卸载:adb unstall 包名  <br>
卸载但是保留数据缓存:  <br>
adb uninstal - k < 包名>    <br>

安装包在安卓端:  <br>
adb shell pm install [路径][ **.apk]  <br>

当然也可以先推进到安卓设备再安装:  <br>
adb push [**.apk] [路径] && adb shell pm install [路径][ **.apk]   <br>


## 八、adb启动页面:  <br>

adb start am -n [包名 +  页面名]   <br>

获取当前页面名: adb shell "dumpsys windows |grep mCurrentFocus"  <br>
package包:android应用唯一的标识  <br>
Activity:应用页面,一个页面就是一个activity  <br>

adb打开页面  <br>
adb logcat ActivityManager:I  | findstr  "cmp"  <br>

## 九、adb 其他命令:  <br>

activity manger:am  <br>
package manger:pm  <br>

获取imei:adb shell service call iphonesubinfo 1  //获取字符串需要处理  <br>
        :adb shell getprop ro.ril.oem.imei       //直接获取imei字符串,不需要处理  <br>
查看当前日期:adb shell date   <br>
查看目录结构:adb shell ls  <br>
查看当前CPU使用情况:adb shell cat/proc/cpuinfo  <br>
查看当前内存使用情况:adb shell cat/proc/meminfo  <br>
查看安卓设备应用 adb shell pm  list packages  
            adb shell pm  list packages -s           //列举系统应用 -s:system  <br>
            adb shell pm  list packages -3           //列举第三方应用  <br>
            adb shell pm  list packages -f           //还能显示出路径  <br>
            adb shell pm list packages  [tencent]    //查看腾讯系软件包(包名带有tencent的)  <br>
借助强大的dump工具, 可以获取很多系统相关信息:  <br>
查看安卓设备硬盘存储空间以及各应用使用存储空间情况: adb shell dumpsys diskstats //剥离应用名字、应用数据大小、应用缓存等信息Cpp代码。  <br>
查看安卓设备状态:adb shell dumpstate  <br>

清除应用数据: adb shell pm clear[包名]  <br>
进入应用缓存数据位置:cd /data/data/[包名]  <br>
截屏并存到/sdcard/:adb shell screencap /sdcard/screen.png   <br>
录屏并存到/sdcard/:adb shell screenrecord /sdcard/demo.mp4 <br>



## 十、安卓设备和PC传输文件(非常常用)  <br>

adb push PC路径 安卓设备路径  <br> 
adb  pull 安卓设备路径 PC路径  <br>

## 十一、adb查看安卓设备日志:adb logcat  <br>

日志的级别:  <br>
V-明细verbose(最低优先级)  <br>
D―调试debug  <br>
l一信息info  <br>
W-警告warn  <br>
E一错误error  <br>
F一严重错误fatal  <br>
S-无记载silent(最高优先级,绝不会输出任何内容)  <br>
adb查看安卓设备日志:adb logcat 后缀分析:  <br>
-v color 使用不同颜色来显示每个优先级  <br>
-f [安卓设备端的文件名] 将日志输出到文件名中,保存到PC端  <br>
adb logcat> pc端文件路径    将日志存放到PC端  <br>
依据条件过滤日志:  <br>
查看日志帮助命令:adb logcat --help  <br>
adb logcat -v time "*:w" 打印warning以及以上级别的日志  <br>
adb logcat ActivityManger:D '*:S' 过滤tag为A..  level 为debug级别以上的日志  <br> 
如果设置的过滤条件为日志级别的话,将会过滤出该等级以及该等级以上等级的日志:  <br>
adb logcat "*:W" 过滤日志级别为W以及已上的日志  <br>

## 十二、模拟按键操作:  <br>


1、模拟按键:adb shell input keyevent [命令] // 括号内一般为数字,不同数字代表不同操作  <br>

2、模拟输入:adb shell input text [文本](仅支持英文,不支持中文和一些符号)  <br>

3、模拟页面的滑动事件:adb shell swipe (x1,y1)(x2,y2)  <br>
   后面可以加时间,单位:毫秒  <br>

4、模拟返回:adb shell input keyevent 4  <br>
   模拟返回主页:adb shell input keyevent 3  <br>

5、模拟拨号:  <br>
按好号码但是不拨出:  adb shell service call phone 1 s16 [ 号码 ]  <br>
按好号码直接拨出:    adb shell am start -a android.intent.action.CALL -d tel:[ 号码 ]  // 如果安卓设备是双卡则需要选择拨出卡号  <br>

6、模拟点击(x,y)处:adb shell tap (x,y)  <br>
左上角为 0 0,右下角则较为大的值(不同安卓设备不同)  v

7、搜狗语音输入:adb shell input tap 656 1513 (误打误撞碰见的)  <br>

8、模拟长按开机键重启安卓设备:adb reboot   

9、模拟开启无线网:adb shell svc wifi enable   <br>
   模拟关闭无线网:adb shell svc wifi disable  

10、启用移动数据: adb shell svc data enable   <br>
    禁用移动数据: adb shell svc data disable  


## 十三、adb 获取性能指标:  

查看当前CPU使用情况:adb shell dumpsys cpuinfo(均值)  

查看当前系统内存使用情况:adb shell  dumpsys meminfo  <br>
查看某个应用内存使用情况:adb shell dumpsys  meminfo [应用包名]      PS:如何获取参照前节  

同linux:top和ps作用是相同的,top是实时动态刷新的  <br>
实时查看进程信息(进程号等,和linux是一样的):adb shell top (实时),adb shell ps -ef  <br>
linux某个进程信息:adb shell top  |grep [包名]  <br>
windows查看进程信息: adb shell top |  findstr [包名]  <br>
top 后面可以加参数-d 1 表示每一秒打印一次  <br>

电池信息:adb shell dumpsys battery   <br>
         adb shell dumpsys batterystats > xxx.txt  //将电池信息保存到XXX.txt中去  <br>
无线网ip:adb shell ip addr show wlan0  | grep -oE 'inet ([0-9]{1,3}.{3})([0-9]{1,3})' //第五位到末尾就是IP地址,末尾可能有空格  <br>
查看本机热点Ip: adb shell ip addr ap0  <br>

性能相关的具体用法,官网:https : / /developer.android.com/ docs  <br>

注意:[   ]不必在命令行中出现,直接写里面的东西即可  

十四、从内容解析器读取值,可以通过其 uri 访问它 

例如:可以使用 uri 获取短信:  <br>
adb shell content query --uri content://sms --projection _id,address,body,read,date,type  <br>
在终端中执行这个命令,你会得到这样的结果  <br>

行:0 _id=2,地址=5554,正文=测试,读取=1,日期=1469533087074,类型=2  <br>

行:1 _id=1, address=5554, body=Hi, read=1, date=1469533011944, type=2  <br>

例如:可以使用特定的 uri 获取特定的短信  <br>

content://sms/inbox   <br>
content://sms/sent  

adb shell content query --uri content://sms/sent --projection _id,address,body,read,date,type  

adb shell content query --uri content://mms/inbox  --projection _id,address,body,read,date,type  


获取通话记录：  
adb shell content query --uri content://call_log/calls  




十五、常见问题自查、解决方式:  <br>
1、查看adb 版本,版本过低的话及时升级版本  <br>
2、查看安卓设备接和PC接口处是否连接好  <br>
3、环境变量是否设置好  <br>
4、手机开发者模式和USB调试开关是否正确打开?  <br>
5、杀掉adb :adb kill-server   <br>
   重启adb :adb start-server  <br>
--一般至此就不会有问题了  

## 十六、其他的安装、学习资料文档  
 
1、最新版本安装网站:https://adbdownload.com/  <br>
2、adb shell 网站:https://adbshell.com/    <br>
3、安卓官方的adb学习文档:https://developer.android.com/studio/command-line/adb?hl=zh-cn  <br>
4、CSDN上比较好的文档:https://blog.csdn.net/lb245557472/article/details/84068519? <br> 
5、adb keyevent 代号:https://www.cnblogs.com/hujingnb/p/10282238.html  <br>
6、ADB git:https://github.com/mzlogin/awesome-adb  <br>
[adb远比我想象中的更好用!!!]  <br>
