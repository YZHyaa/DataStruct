## 1.AVL树
### 1.1 基本概念
1. 发明者：G.M.Adlson-Velsky 和 Evgenii Landis 所以称之为 AVL 树
2. 优点：AVL树是带有平衡条件的查找二叉树。他**保证树的深度为O(logN)**，查找复杂度O（logN）
3. 缺点：要不断旋转来保证平衡，在多增删情况下耗费资源大   ---> 近似平衡树

### 1.2 平衡因子：判断平衡的标准

* 平衡因子 = 左子树高度 - 右子树高度（有时相反）
* 取值： { -1，0，1}

AVL树所有节点的平衡因子必须是{-1,0,1}，在CRUD时要维护节点的平衡因子

<img src="https://img-blog.csdnimg.cn/20201006024825717.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center" width="75%">


### 1.3 旋转操作：实现平衡的手段
AVL维护平衡因子的手段：四种旋转。下面我们就逐个来看：

**1.左旋**
* 定义：左旋全称其实是叫围绕某个节点的左旋

	![在这里插入图片描述](https://img-blog.csdnimg.cn/20201006023222380.png#pic_center)
* 使用场景：子树形态是右右子树（子孙节点都在右边）

	<img src="https://img-blog.csdnimg.cn/20201006023445746.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center" width="75%">

* 应用举例：

	![这里写图片描述](https://img-blog.csdn.net/2018040818502780?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1ODA2ODYz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**2.右旋**
* 定义：右旋即围绕某个节点的右旋

	![在这里插入图片描述](https://img-blog.csdnimg.cn/20201006023631842.png#pic_center)

* 使用场景：子树形态是左左子树（子孙节点都在左边）

	<img src="https://img-blog.csdnimg.cn/20201006023739581.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center" width="75%">

* 应用举例：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201006023903393.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center)

**3.左右旋**
* 定义：左右旋，顾名思义就是先左旋再右旋；
* 使用场景：子树形态是左右子树（子节点在左边，孙节点在右边）
<img src="https://img-blog.csdnimg.cn/20201006024610724.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center" width="75%">
<img src="https://img-blog.csdnimg.cn/20201006024659681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center" width="75%">

* 应用举例：
	![这里写图片描述](https://img-blog.csdn.net/20180408185138569?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1ODA2ODYz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**4.右左旋**
* 定义：右左旋，顾名思义就是先右旋再左旋
* 使用场景：子树形态是右左子树（子节点在右边，孙节点在左边）

	<img src="https://img-blog.csdnimg.cn/20201006025455989.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzkzNTkyNw==,size_16,color_FFFFFF,t_70#pic_center" width="75%">

* 应用举例：
![这里写图片描述](https://img-blog.csdn.net/20180408185158184?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1ODA2ODYz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
## 2.红黑树
红黑树是一种**近似平衡的二叉搜索树**，它能够确保任何一个节点的左右子树高度的高度差小于两倍

- 每个节点要么是红色，要么是黑色
- 根节点是黑色的
- 每个叶子节点都是黑色的空节点（NIL），也就是说，叶子节点不存储数据
- 任何相邻的节点都不能同时为红色，也就是说，红色节点是被黑色节点隔开的
- 每个节点，从该节点到达其可达叶子节点的所有路径，都包含相同数目的黑色节点

红黑树的高度近似 log n，所以它是近似平衡，插入、删除、查找操作的时间复杂度都是 O(logn）

## 3.红黑树与AVL树比较
1. AVL 树是一种高度平衡的二叉树，所以查找的效率非常高
2. AVL 树为 了维持这种高度的平衡，就要付出更多的代价。每次插入、删除都要做调整，就比较复杂、 耗时
3. 红黑树的插入、删除、查找各种操作性能都比较稳定
4. 红黑树只是做到了近似平衡，并不是严格的平衡，所以在维护平衡的成本上，要比 AVL 树 要低

