# Dalvik 虚拟机参数调优【Heap】

>Android设备出厂以后，虚拟机对单个应用的最大内存分配就确定下来了，超出这个值就会OOM。这个属性值是定义在/system/build.prop文件中的；

## Dalvik 空间相关

### heapstartsize

```shell
[dalvik.vm.heapstartsize]: [8m]
```

它表示APP运行的时候分配给虚拟机堆分配的初始大小，它会影响到整个系统对RAM的使用程度，和第一次使用应用时的流畅程度。它值越小，系统ram消耗越慢，但一些较大应用一开始不够用，需要调用gc和堆调整策略，导致应用反应较慢。它值越大，这个值越大系统ram消耗越快，但是应用更流畅，可减少一次内存扩容申请的次数；

### heapgrowthlimit

```shell
[dalvik.vm.heapgrowthlimit]: [384m]
```

初始分配给APP虚拟机的内存不够用的时候会自动扩容，这个值就是扩容的最大值。（扩容的内存是APP又向系统要的）

表示单个应用可用最大内存；主要对应的是这个值，它表示单个进程内存被限定在384m,即程序运行过程中实际只能使用384m内存，超出就会报OOM。（仅仅针对dalvik堆，不包括native堆）

【案例】： 高负载场景，HeapTaskDeamon跑的比较多；

修改 dalvik.vm.heapgrowthlimit=384m 参数可以减少heaptaskdeamon； 

另外在是8G内存的情况下，市场dalvik.vm.heapgrowthlimit参数的值都是384m

### heapsize

```shell
[dalvik.vm.heapsize]: [512m]
```

APP使用大heap时的限定值。

大heap ：APP的mainfest文件中有声明 **android:largeHeap=“ture”**

这时堆空间的最大值使用heapsize限定；没有声明largeHeap的APP，堆空间使用heapgrowthlimit限定



heapsize表示不受控情况下的极限堆，表示单个虚拟机或单个进程可用的最大内存。而android上的应用是带有独立虚拟机的，也就是每开一个应用就会打开一个独立的虚拟机（这样设计就会在单个程序崩溃的情况下不会导致整个系统的崩溃）



【注意】：

- heapsize参数表示单个进程可用的最大内存，但如果存在heapgrowthlimit参数，则以heapgrowthlimit为准。
- 在android开发中，如果要使用大堆，需要在manifest中指定android:largeHeap为true，这样dvm heap最大可达heapsize。

-  **dalvik.vm.heapsize这个property只对largeHeap的app起作用。当app的AndroidManifest.xml设置了大内存，heapGrowthLimit就不起作用了**



## GC相关

### heaptargetutilization

```shell
[dalvik.vm.heaptargetutilization]: [0.75]
```

描述堆内存的利用率，是当前的有效变量所占内存和总内存的比值。其取值位于0与1之间。当GC进行完垃圾回收之后，Dalvik的堆内存会进行相应的调整，通常结果是当前存活的对象的大小与堆内存大小做除法，得到的值为这个选项的设置，即这里的0.75.



### heapminfree

```shell
[dalvik.vm.heapminfree]: [2m]
```

描述单次堆内存调整的最小值



### heapmaxfree

```shell
[dalvik.vm.heapmaxfree]: [8m]
```

描述单次堆内存调整的最大值