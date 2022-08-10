# Entry接口下的实现类
- SimpleEntry ：初始化后可以setValue()设置值
- SimpleImmutableEntry ：初始化后不能再改变
## SimpleEntry
SimpleEntry可以存储单键值对，一个对象只存储一个键值对
![在这里插入图片描述](https://img-blog.csdnimg.cn/436352d92a404a29be748892fdc62668.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/70fa9ad92327443db3a4e7dcf6378a8f.png)
SImpleEntry的使用
构造方法赋值
```java
public static void main(String[] args) {
	//构造方法赋值
    AbstractMap.SimpleEntry<String,String> entry1 = new AbstractMap.SimpleEntry<>("刘备","蜀汉" );
    AbstractMap.SimpleEntry<String,String> entry2 = new AbstractMap.SimpleEntry<>("孙权","东吴" );
    System.out.println(entry1);
    System.out.println(entry2);
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/8f0f06bb1358416eaf4049430197a182.png)
可以修改对象的值
![在这里插入图片描述](https://img-blog.csdnimg.cn/91698b64d7754374aae2474a66c7a4f2.png)
应用：
需要单键值对存储的场景
[LeetCode102 二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)

```java
class Solution {
    /**
     * 1.root入队
     * 2.遍历队列
     * 3.取出队首元素 level,node
     * 4.left,right 入队
     */
    public List<List<Integer>> levelOrder(TreeNode root) {
        //返回的list
        List<List<Integer>> res = new ArrayList<>();

        if(root == null){
            return res;
        }
        Queue<AbstractMap.SimpleEntry<TreeNode,Integer>> queue = new LinkedList<>();

        //1.root入队
        queue.add(new AbstractMap.SimpleEntry(root,0));

        //2、遍历队列
        while (!queue.isEmpty()){
            //3.取出队首元素
            AbstractMap.SimpleEntry<TreeNode,Integer> map = queue.poll();
            //取出节点和对应的层数
            TreeNode node = map.getKey();
            Integer level = map.getValue();

            if(res.size() == level){
                res.add(new ArrayList<>());
            }
            //拿到对应层数的List
            List<Integer> list = res.get(level);
            list.add(node.val);

            //4.left , right 入队
            if(node.left != null){
                queue.add(new AbstractMap.SimpleEntry(node.left,level+1));
            }
            if(node.right != null){
                queue.add(new AbstractMap.SimpleEntry(node.right,level+1));
            }
        }
        return res;
    }
}
```


## SimpleImmutableEntry
初始化后就不能再修改，修改会报错
```java
AbstractMap.SimpleImmutableEntry<String,String> entry = 
						new AbstractMap.SimpleImmutableEntry<>("刘备", "蜀汉");
      entry.setValue("NULL");
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/d92b5776630a498d86dc3a85f7a1a5a0.png)

# Pair
初始化后不能修改
![在这里插入图片描述](https://img-blog.csdnimg.cn/747b02a5073444cf887b6277d627a58e.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/d964dcc832ab47f393c140323350fbfc.png)




