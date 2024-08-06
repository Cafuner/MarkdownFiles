# Morris遍历算法

参考：https://blog.csdn.net/x0919/article/details/120602597

在遍历二叉树时，有递归和非递归两种经典方式。无论是哪一种，空间复杂度都是 $O(n)$ 。Morris遍历算法利用了线索二叉树的思想，利用空指针域存储后继节点，达到空间复杂度 $O(1)$ 的二叉树遍历算法。

在讲解Morris遍历算法前，我们先来分析一下为什么传统的算法需要压栈。对于一个节点，总共有3个部分需要遍历，根节点，左子树和右子树。假设当我们遍历完左子树后，需要遍历根节点或根节点的右子树，那么此时应该如何知道下一个要访问的节点的地址呢？为了解决这个问题，我们事先用一个栈存储了下一个要访问的节点，这样从左子树退出时，只需要从栈顶拿出下一个节点即可。

那么，我们是否可以不用事先存储，在访问完左子树就能回到根节点呢？而且，回到根节点之后我们还需要知道左子树是否已经被访问过，这样我们就可以去访问根节点或右子树（避免重复访问左子树）。Morris 遍历算法解决了这个问题。它利用线索二叉树的思想，利用空指针域存储后继，这样在访问完左子树后，就能够回到根节点。

下面是具体的算法：

我们准备两个引用变量：cur和mostRight。 cur表示当前遍历的节点；mostRight表示cur节点左子树中，往右边遍历，第一个没有右孩子的节点。遍历算法如下：

+ 如果cur没有左孩子，cur就向右孩子移动。（cur = cur.right）
+ 如果cur有左孩子，那就找到cur左子树中，最靠右的节点mostRight：
    + 如果mostRight的右孩子是null，说明是第一次遍历到mostRight。此时让mostRight的右孩子指向cur。
    + 如果mostRight的右孩子是cur，说明是第二次遍历到mostRight。此时让mostRight的右孩子等于null即可。
+ 遍历结束条件：cur等于null时

>值得注意的是，当第一次遍历到mostRight时，让mostRight的右孩子指向cur之后，cur 再往左子树走，然后应该再写一句continue。让代码继续往cur的左子树继续遍历。着重要分清楚什么时候是第一次遍历到mostRight，什么是第二次遍历到mostRight。简单点说，第一次就是mostRight的右孩子是null时，此时我们需要将右孩子指向cur；第二次是mostRight的右孩子指向cur的时候，此时我们需要将右孩子重新改回来，改为null。

```java
public static void morris(Node head) {
    if (head == null) {
        return;
    }

    Node cur =  head;
    Node mostRight = null;
    while (cur != null) {
        mostRight = cur.left;
        if (mostRight != null) { //有左子树的情况
            //查找最靠右的节点
            while (mostRight.right != null && mostRight.right != cur) {
                mostRight = mostRight.right;
            }

            //上面循环停下来，要么是该节点右孩子为null，要么就是为cur的情况
            if (mostRight.right == null) { //第一次来到这个最右节点
                mostRight.right = cur;
                cur = cur.left;
                continue;
            } else { //第二次来到这个最右节点
                mostRight.right = null;
            }
        }
        cur = cur.right; //没有左子树的情况，就转向右子树
    }
}
```



## Morris先序遍历

Morris改前序遍历，非常简单。记住两个点：

- 如果cur节点没有左孩子，那就访问当前cur的值
- 如果是第一次遍历到mostRight节点，那就访问当前cur的值

```java
public static void morrisPre(Node head) {
    if (head == null) {
        return;
    }

    Node cur =  head;
    Node mostRight = null;
    while (cur != null) {
        mostRight = cur.left;
        if (mostRight != null) { //有左子树的情况
            //查找最靠右的节点
            while (mostRight.right != null && mostRight.right != cur) {
                mostRight = mostRight.right;
            }

            //上面循环停下来，要么是该节点右孩子为null，要么就是为cur的情况
            if (mostRight.right == null) { //第一次来到这个最右节点
                System.out.print(cur.val + " ");
                mostRight.right = cur;
                cur = cur.left;
                continue;
            } else { //第二次来到这个最右节点
                mostRight.right = null;
            }
        } else { //没有左子树的情况
            System.out.print(cur.val + " ");
        }

        cur = cur.right; //没有左子树的情况，就转向右子树
    }

}
```





## Morris中序遍历

中序跟先序的遍历很相似，也是记住两个点即可：

+ 如果cur没有左孩子，直接访问cur的值
+ 如果mostRight是第二次遍历到，那就访问cur的值。跟前序遍历的区别只是mostRight这里，前序是第一次遍历到mostRight就访问cur，中序是第二次遍历到才访问，因为第二次访问到mostRight时说明左子树已经全部访问过，下一步要访问根节点了。

```java
public static void morrisIn(Node head) {
    if (head == null) {
        return;
    }

    Node cur =  head;
    Node mostRight = null;
    while (cur != null) {
        mostRight = cur.left;
        if (mostRight != null) { //有左子树的情况
            //查找最靠右的节点
            while (mostRight.right != null && mostRight.right != cur) {
                mostRight = mostRight.right;
            }

            //上面循环停下来，要么是该节点右孩子为null，要么就是为cur的情况
            if (mostRight.right == null) { //第一次来到这个最右节点
                mostRight.right = cur;
                cur = cur.left;
                continue;
            } else { //第二次来到这个最右节点
                mostRight.right = null;
                System.out.print(cur.val + " ");
            }
        } else {
            System.out.print(cur.val + " ");
        }

        cur = cur.right; //没有左子树的情况，就转向右子树
    }
}
```



## Morris后序遍历

Morris改后序遍历，稍微有一点难，因为Morris遍历的结果满足一个规律：当前节点没有左子树，那么只会遍历一次这个节点；当前节点有左子树，那么会有mostRight节点会返回到cur节点，也就是说有左孩子的节点，会遍历两次。后序遍历是每个节点，遍历到第3次的时候才访问，现在Morris遍历最多只能遍历到2次，那该如何是好？

其实，仔细分析可以知道，在访问一个树时，访问的最后一步一定是从这个子树的最右子节点开始，沿着最右侧的路径挨个节点访问，直到到达根节点。我们约定每次访问一个树之后，就是按照后序遍历一个树，但是除了这个树最右侧的这些节点。也就是说，当我们第二次回到cur节点时，cur的左子树没有完全被访问，其中只有以cur.left为根的最右侧的路径的节点没有被访问。因此，当我们第二次回到cur节点，就需要按照倒序访问以cur.left为根的最右侧路径。这里要用到反转单链表的算法，同样空间复杂度是 $O(1)$ 。在访问完后还需要将单链表反转回来。此时，左子树才被完全访问。接下来我们要访问右子树。由于我们已经约定了访问一个树后，不需要访问以其根节点为根的最右侧路径，因此我们不需要考虑访问完右子树再回到cur节点，只需要保证右子树除了最右侧路径外都被访问过即可。因此，此时只需要将cur移动到cur.right即可。

```java
public static void morrisPost(Node head) {
    if (head == null) {
        return;
    }

    Node cur =  head;
    Node mostRight = null;
    while (cur != null) {
        mostRight = cur.left;
        if (mostRight != null) { //有左子树的情况
            //查找最靠右的节点
            while (mostRight.right != null && mostRight.right != cur) {
                mostRight = mostRight.right;
            }

            //上面循环停下来，要么是该节点右孩子为null，要么就是为cur的情况
            if (mostRight.right == null) { //第一次来到这个最右节点
                mostRight.right = cur;
                cur = cur.left;
                continue;
            } else { //第二次来到这个最右节点
                mostRight.right = null;
                printList(cur.left);
            }
        }
        cur = cur.right; //没有左子树的情况，就转向右子树
    }
    printList(head); //最后还得打印整棵树，最靠右的节点
}
```
