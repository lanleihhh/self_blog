# B树
---
数据库中数据量过大时，平衡二叉树由于深度过大，造成磁盘IO次数多，效率低。

B树也叫**平衡多路查找树**，它的出现就是为了减少磁盘IO操作，思想是降低树的高度，从==“瘦高”-->“矮胖”==。

核心是让每个节点**承载更多的元素**，**拥有更多的孩子**。

**优点**：改善数据库的查询效率，减少了对磁盘的IO操作次数
**缺点**：不便于范围查询

---

一个m阶的B树：单一节点拥有最多子节点的数量，称为B树的阶。

## 一个m阶B树的具有的特征（或必须满足的条件）

1. 如果根节点不是叶子节点，那么它至少有两个节点。

2. 所有叶子节点位于同一层(高度一致)。

3. 除根节点和叶子节点之外，一个节点至少拥有`ceil( m / 2)`个节点）。

   `ceil()   :向上取整，ceil(3/2)=ceil(1.5)=2`

4. 每个叶子节点包含`k-1`个元素，`ceil(m/2)<=  k <=m `。

   `若m=3,  2<=k<=3, 1<=(k-1)<=2,也就是3阶的树叶子节点包含1~2个元素`

5. 拥有`k`个节点的非叶子节点，包含`k-1`个元素。


---
## B树的查找

如图是一颗3阶B树，满足B树的特征

![在这里插入图片描述](https://img-blog.csdnimg.cn/5ba53a99521248eb85c38c999fe4ee19.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


查找元素5

第一步

![在这里插入图片描述](https://img-blog.csdnimg.cn/747770ad9fd34feeb1bf1e320d09294f.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


第二步

![在这里插入图片描述](https://img-blog.csdnimg.cn/201b8377ee2144d9b24dfaf5982ba055.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


第三步

![在这里插入图片描述](https://img-blog.csdnimg.cn/0524a41400b5475dba240ae39c192995.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)


找到节点中的元素5

---
## B树插入元素(一定是在叶子节点插入)
### 1.插入后，没有破坏B树的规则
![在这里插入图片描述](https://img-blog.csdnimg.cn/427d6795685d44889983c095c606413e.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_19,color_FFFFFF,t_70,g_se,x_16)
插入元素`10`。
![在这里插入图片描述](https://img-blog.csdnimg.cn/9ea93a322ef440979707cef978d6f70d.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
插入10，并没有破坏B树的规则
### 2.插入后，叶子节点元素超过m-1个
叶子节点所能容纳的元素数为`ceil(m/2)-1~(m-1)`，超过m-1,会破坏B树的规则。
例如：插入元素4
![在这里插入图片描述](https://img-blog.csdnimg.cn/e43b6890ccc94a21b44b3ddfcfc72083.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
对于3阶B树也就是叶子节点可以容纳1~2个元素，插入4后破坏 3阶B树规则，因此需要进行**节点分裂**。==分裂后，将`ceil(m/2)`位置的元素上升到父节点。==
![在这里插入图片描述](https://img-blog.csdnimg.cn/ee84358243a247d1bb6b66561ca6853f.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
这时，父节点(2,4,6)依旧是破坏B树规则，所以继续对父节点进行分裂

![在这里插入图片描述](https://img-blog.csdnimg.cn/a37ffd1efdb34b649d89b766862d063d.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
分裂到满足B树规则时，插入就完成了。
## B树删除元素
### 1.删除叶子节点上的元素，没有破坏规则
删除元素3
![在这里插入图片描述](https://img-blog.csdnimg.cn/600b314c702f4b7596e7e487c495b779.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
叶子节点可以容纳1~2个元素，没有破坏规则。

### 2.删除叶子节点上的元素，剩余元素不足，破坏B树规则，但是相邻兄弟节点有多余元素
删除元素8

![在这里插入图片描述](https://img-blog.csdnimg.cn/f9c0a8f854d34e6aa26519a4ab3281d3.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
删除8后，（2,6)节点有两个节点，但是却有两个元素，不满足规则
`拥有k个节点的非叶子节点，包含k-1个元素`

---


- 为了不打破规则，我们向8的兄弟节点(3,5)借1个元素
==不能直接将5放到8的位置，不符合平衡树的规则==
![在这里插入图片描述](https://img-blog.csdnimg.cn/c5f8f84adf414e0381801f30f6b43c97.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)

需要先将借来的元素`5`上升到父节点中，再将`5`与`8`之间的元素`6`移动到原来`8`的位置
![在这里插入图片描述](https://img-blog.csdnimg.cn/5dd85af5efd642eb852c2f4478f566cb.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
### 3.删除元素在叶子节点，剩余元素不足，破坏B树规则，相邻兄弟也没有多余元素
删除元素3
![在这里插入图片描述](https://img-blog.csdnimg.cn/d86070e67768411f9d83106c57d63aca.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)

---
解决办法：相邻元素合并

![在这里插入图片描述](https://img-blog.csdnimg.cn/b6fd898df48a49f8ad8dae1e511a22a2.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)

---
### 4.删除的中间节点的元素
删除元素12
![在这里插入图片描述](https://img-blog.csdnimg.cn/59166291e30a48338a81e7c268c15c3f.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)
使用该元素的前驱(11)or后继(13)元素替换它的位置

---

- 前驱元素替换
	![在这里插入图片描述](https://img-blog.csdnimg.cn/07f9830d6aad47fca83db022ad5ab353.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_19,color_FFFFFF,t_70,g_se,x_16)
由于除root和叶子节点外，一个节点拥有的节点数至少为ceil(3/2)=2，所以要将(3,5)此节点分为两个节点
	![在这里插入图片描述](https://img-blog.csdnimg.cn/3657b592da4d45ad90e0b3381e87dda6.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_18,color_FFFFFF,t_70,g_se,x_16)
---

- 后继元素替换
	![在这里插入图片描述](https://img-blog.csdnimg.cn/65118b6417d040b68bfe8c5a42c8edaa.png?x-oss-process=image,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGFubGVpaGho,size_20,color_FFFFFF,t_70,g_se,x_16)





