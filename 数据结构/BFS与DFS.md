BFS与DFS
- BFS：(Breadth-First-Search )广度优先搜索,<font color = blue>使用队列实现</font>，<font color = red>寻找最短路径</font>

- DFS：(Depth-First-Search)   深度优先搜索，<font color = blue>使用递归实现</font>，<font color = red>快速发现底部节点</font>

BFS：
![在这里插入图片描述](https://img-blog.csdnimg.cn/da6352c539b74dfeae5cbcd08d0f9397.gif#pic_center)
DFS：
![在这里插入图片描述](https://img-blog.csdnimg.cn/aae4f050768a4228849d2940048e2da2.gif#pic_center)
# 树中的BFS与DFS
BFS应用：树的层序遍历：队列

```java
public class BinarySearchTree<T extends Comparable<T>> {
    //树的结点
    class Node {
        //结点的内容
        private T val;
        //左孩子结点
        private Node left;
        //右孩子结点
        private Node right;

        public Node(T val) {
            this.val = val;
        }
    }

	//层序遍历  (广度优先)
    public List<T> layerOrder() {
        List<T> list = new ArrayList<>();
        if (root != null) {
            Queue<Node> queue = new LinkedList<>();
            queue.add(root);
            while (!queue.isEmpty()) {
                //从队列取出队首元素
                Node node = queue.poll();
                list.add(node.val);
                if (node.left != null) {
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    queue.offer(node.right);
                }
            }
        }
        return list;
    }
}
```
DFS：深度优先，树的中序遍历（以及前序，后序）

```java
	//中序遍历
    public List<T> middleOrder() {
        //存储从树中遍历的值
        List<T> res = new ArrayList<>();
        //使用递归遍历
        middleOrder(root, res);
        //返回存储树中元素的集合
        return res;
    }

    //中序遍历   递归
    private void middleOrder(Node root, List<T> res) {
        //递归到底的情况
        if (root == null) {
            return;
        }
        //递归操作
        //先遍历左树,再遍历root,然后遍历右树
        middleOrder(root.left, res);
        res.add(root.val);
        middleOrder(root.right, res);
    }
```
# 图中的BFS和DFS
待补充...
