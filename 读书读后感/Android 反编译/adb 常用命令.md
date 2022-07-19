#### 1、cat 查看 文件内容

#### 2、touch 文件名  创建文件

#### 3、echo "111" > 文件 覆盖之前的  >> 追加

#### 4、adb shell dumpsys activity top  查看activity 信息 

#### 5、grep 过滤  findstr 

#### 6、adb dumpsys package  应用名称

#### 7、adb dumpsys meminfo 应用名称|pid

#### 8、adb dumpsys dbinfo 应用名称|pid  数据库相关信息

#### 9、adb install xx.apk  |adb install -r xx.apk  升级更新

#### 10、adb uninstall  xx.apk 卸载应用

#### 11、adb pull /sdcard/xx.txt  D:/   拉去文件到本地

#### 12 、adb push D:/  /sdcard/    本地文件到设备

#### 13、adb shell screencap -p  截图文件路径

#### 14、adb shell screenrecord /sdcard/xx.mp4  录视频

#### 15、adb shell input text 输入文字 、模拟物理按键、滚动

#### 16、adb forward 端口转发  adb forward tcp:1212  tcp:343

#### 17、adb jdwp 查看设别中可以调试的 进程号

#### 18、adb logcat -s tag 查看log 过滤tag

#### 19、adb shell pm clear  包名  清除数据

#### 20、run-as 包名  查看沙盒数据

#### 21、ps |grep过滤内容  ps -t pid

#### 22、adb shell am  start -n 包名 启动应用

#### 23、adb shell am startservice -n 包名/服务

#### 24、adb shell am boardcast -a action 发送广播

#### 25、netcfg 查看网络ip 

#### 26、netsatrt 

#### 27、top 查看cpu  -m 最多显示几个进程 -n 刷新次数  -d 刷新间隔时间  -s  按照那列排序

-t 显示线程信息部显示进程

#### 28、getprop ro.debug 查看系统配置信息

#### 29、cat /proc/pid/maps 内存信息

#### 30、cat /proc/pid/status 进程状态信息

#### 31、adb tcpip 5555  | adb connect 192.168.1.X 远程连接手机

