# 二叉树

## 二叉树遍历

&emsp;&emsp;遍历二叉树的每个节点。

二叉树结构如下：

~~~ java
public class Node {
    public int value;
    public Node left;
    public Node right;

    public Node(int data) {
        this.value = data;
    }
}
~~~

### 先序遍历

&emsp;&emsp;指以先序的方式遍历二叉树的每个节点，结构为根左右。二叉树遍历每一个节点时，该节点都会被访问三次，先序的含义即第一次被访问的节点。

#### 递归形式

~~~ java
public void preOrderRecur(Node head) {
    if(head == null) return;
    System.out.println(head.value);
    preOrderRecur(head.left);
    preOrderRecur(head.right);
}
~~~

#### 非递归形式

&emsp;&emsp;非递归遍历逻辑：使用栈的数据结构,使用当前树节点变量指向根节点，然后将当前树节点点压入栈中，然后进入循环，循环的条件为：栈不为空。弹出当前栈顶的树节点并使用当前树节点变量指向它。判断当前树节点是否存在右子树节点若存在，则将右子树节点压入栈中。否则无操作。判断当前树节点是否存在左子树节点,若存在，则将左子树节点压入栈中。否则无操作。

~~~ java
/**
滴滴视频面试的测试题
**/
public void preOrderUnRecur(Node head) {
    Node cur = head;
    Stack<Node> stack = new Stack<>();
    // 将根节点先压入栈中
    stack.push(cur);
    // 循环处理
    while(!stack.isEmpty()) {
        // 访问节点
        cur = stack.pop();
        // 打印节点的值
        System.out.println(cur.val);
        // 如果右子树不为空则压入栈中，保证栈的弹出顺序为树的先序遍历顺序，即根左右
        if(cur.right != null) {
            stack.push(cur.right);
        }
        // 如果左子树不为空，则压入栈中
        if(cur.left != null) {
            stack.push(cur.left);
        }
    }
}

~~~

### 中序遍历

&emsp;&emsp;指以先序的方式遍历二叉树的每个节点，结构为根左右。二叉树遍历每一个节点时，该节点都会被访问三次，先序的含义即第二次被访问的节点。 格式为：左右根

#### 递归形式

~~~ java
public void inOrderRecur(Node head) {
    if(head == null) return;
    preOrderRecur(head.left);
    System.out.println(head.value);
    preOrderRecur(head.right);
}
~~~

#### 非递归形式

&emsp;&emsp;逻辑：

1. 申请一个新的栈，记 stack。 初始时，令变量 cur = head。

2. 先把 cur 节点压入栈中，对以 cur 节点为头结点的整棵子树来说，依次把左边界压入栈中，即不停地令 cur = cur.left, 然后重复 2.
3. 不段重复步骤 2， 直到发现 cur为空， 此时从 stack 中弹出一个节点，记为 node。打印 node 的值，并且让 cur = node.right， 然后继续重复步骤 2.
4. 当 stack 为空且 cur 为空时，整个过程停止。

~~~ java
public void inOrderRecur(Node head) {
    if(head == null) return;
    cur = head;
    // 申请新的栈
    Stack<Node> stack = new Stack<>();
    // 当栈不为空或 cur 节点不为空时
    while(!stack.isEmpty() || cur != null) {
        // 若当前节点不为空，则将当前节点压入栈中，并将当前节点指向当前节点的左子树
        if(cur != null) {
            stack.push(cur);
            cur = cur.left;

        } 
        // 若当前节点为叶子节点的空左子树 | 若当前节点为叶子结点空右子树
        else {
            // 弹出栈中叶子节点 | 弹出叶子结点的父节点
            cur = stack.pop();
            // 打印节点的值 | 打印叶子结点的父节点的值
            System.out.println(head.value);
            // 将叶子节点的右空子树赋给 cur | 将父节点的右子树赋给 cur
            cur = cur.right;
        }
    }
}

~~~

### 后序遍历

&emsp;&emsp;指以先序的方式遍历二叉树的每个节点，结构为根左右。二叉树遍历每一个节点时，该节点都会被访问三次，先序的含义即第二次被访问的节点。 格式为：左右根

### 神级遍历方法
