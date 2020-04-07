---
title: JVM GC test
comments: true
date: 2020-03-24 10:57
categories: [Java]
tags: [Java]
---

## java 版本
$ java -version
java version "1.8.0_221"
Java(TM) SE Runtime Environment (build 1.8.0_221-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.221-b11, mixed mode)

## 启动
```
java -server \
-XX:+UseConcMarkSweepGC -XX:+UseParNewGC \
-verbose:gc -XX:+PrintGCDetails -XX:+PrintGCDateStamps \
-Xloggc:/data/logs/jvm-gc.log \
-XX:+HeapDumpOnOutOfMemoryError \
-jar test.jar
```

```s
MinHeapFreeRatio=40  #对应jvm启动参数-XX:MinHeapFreeRatio设置JVM堆最小空闲比率(default 40)
MaxHeapFreeRatio=70  #对应jvm启动参数 -XX:MaxHeapFreeRatio设置JVM堆最大空闲比率(default 70)
MaxHeapSize=512.0MB  #对应jvm启动参数-XX:MaxHeapSize=设置JVM堆的最大大小
NewSize  = 1.0MB     #对应jvm启动参数-XX:NewSize=设置JVM堆的‘新生代’的默认大小
MaxNewSize =4095MB   #对应jvm启动参数-XX:MaxNewSize=设置JVM堆的‘新生代’的最大大小
OldSize  = 4.0MB     #对应jvm启动参数-XX:OldSize=<value>:设置JVM堆的‘老生代’的大小
NewRatio  = 8        #对应jvm启动参数-XX:NewRatio=:‘新生代’和‘老生代’的大小比率
SurvivorRatio = 8    #对应jvm启动参数-XX:SurvivorRatio=设置年轻代中Eden区与Survivor区的大小比值
PermSize= 16.0MB     #对应jvm启动参数-XX:PermSize=<value>:设置JVM堆的‘永生代’的初始大小
MaxPermSize=64.0MB   #对应jvm启动参数-XX:MaxPermSize=<value>:设置JVM堆的‘永生代’的最大大小
```


##  jmap -heap 26313
```java
Attaching to process ID 26313, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 25.221-b11

using parallel threads in the new generation.
using thread-local object allocation.
Concurrent Mark-Sweep GC

Heap Configuration:
   MinHeapFreeRatio         = 40
   MaxHeapFreeRatio         = 70
   MaxHeapSize              = 262144000 (250.0MB)
   NewSize                  = 5570560 (5.3125MB)
   MaxNewSize               = 86638592 (82.625MB)
   OldSize                  = 11206656 (10.6875MB)
   NewRatio                 = 2
   SurvivorRatio            = 8
   MetaspaceSize            = 21807104 (20.796875MB)
   CompressedClassSpaceSize = 1073741824 (1024.0MB)
   MaxMetaspaceSize         = 17592186044415 MB
   G1HeapRegionSize         = 0 (0.0MB)

Heap Usage:
New Generation (Eden + 1 Survivor Space):
   capacity = 5046272 (4.8125MB)
   used     = 1948424 (1.8581619262695312MB)
   free     = 3097848 (2.9543380737304688MB)
   38.611156909496756% used
Eden Space:
   capacity = 4521984 (4.3125MB)
   used     = 1521368 (1.4508895874023438MB)
   free     = 3000616 (2.8616104125976562MB)
   33.64381651947464% used
From Space:
   capacity = 524288 (0.5MB)
   used     = 427056 (0.4072723388671875MB)
   free     = 97232 (0.0927276611328125MB)
   81.4544677734375% used
To Space:
   capacity = 524288 (0.5MB)
   used     = 0 (0.0MB)
   free     = 524288 (0.5MB)
   0.0% used
concurrent mark-sweep generation:
   capacity = 23826432 (22.72265625MB)
   used     = 16323328 (15.567138671875MB)
   free     = 7503104 (7.155517578125MB)
   68.50932611311673% used

16730 interned Strings occupying 1551928 bytes.
```

## jstat -gccapacity 26313 2s 10
```
 NGCMN    NGCMX     NGC     S0C   S1C       EC      OGCMN      OGCMX       OGC         OC       MCMN     MCMX      MC     CCSMN    CCSMX     CCSC    YGC    FGC
  5440.0  84608.0   5440.0  512.0  512.0   4416.0    10944.0   171392.0    23268.0    23268.0      0.0 1083392.0  39472.0      0.0 1048576.0   5168.0    123     6
  5440.0  84608.0   5440.0  512.0  512.0   4416.0    10944.0   171392.0    23268.0    23268.0      0.0 1083392.0  39472.0      0.0 1048576.0   5168.0    123     6
  5440.0  84608.0   5440.0  512.0  512.0   4416.0    10944.0   171392.0    23268.0    23268.0      0.0 1083392.0  39472.0      0.0 1048576.0   5168.0    123     6
  5440.0  84608.0   5440.0  512.0  512.0   4416.0    10944.0   171392.0    23268.0    23268.0      0.0 1083392.0  39472.0      0.0 1048576.0   5168.0    123     6
  5440.0  84608.0   5440.0  512.0  512.0   4416.0    10944.0   171392.0    23268.0    23268.0      0.0 1083392.0  39472.0      0.0 1048576.0   5168.0    123     6
  5440.0  84608.0   5440.0  512.0  512.0   4416.0    10944.0   171392.0    23268.0    23268.0      0.0 1083392.0  39472.0      0.0 1048576.0   5168.0    123     6
  5440.0  84608.0   5440.0  512.0  512.0   4416.0    10944.0   171392.0    23268.0    23268.0      0.0 1083392.0  39472.0      0.0 1048576.0   5168.0    123     6
  5440.0  84608.0   5440.0  512.0  512.0   4416.0    10944.0   171392.0    23268.0    23268.0      0.0 1083392.0  39472.0      0.0 1048576.0   5168.0    123     6
  5440.0  84608.0   5440.0  512.0  512.0   4416.0    10944.0   171392.0    23268.0    23268.0      0.0 1083392.0  39472.0      0.0 1048576.0   5168.0    123     6
  5440.0  84608.0   5440.0  512.0  512.0   4416.0    10944.0   171392.0    23268.0    23268.0      0.0 1083392.0  39472.0      0.0 1048576.0   5168.0    123     6
```

