# Interview-Question
iOS 面试题积累 - 算法篇

### 索引

1. [递归计算1到100的和](./algorithm.md#1-递归计算1到100的和)

2. [设计一个递归搜索制定目录下指定类型文件到函数，要求写清返回值、函数名、参数列表。*只写函数名，不用写函数内容。](./algorithm.md#2-设计一个递归搜索制定目录下指定类型文件到函数要求写清返回值函数名参数列表只写函数名不用写函数内容)
3. [n个数不相等，求其中的max、min，考虑比较次数，最优的比较次数是多少？](./algorithm.md#3-n个数不相等求其中的maxmin考虑比较次数最优的比较次数是多少)
4. [利用rand7（可以产生1-7的随机数），求rand13（要求是均等）](./algorithm.md#4-利用rand7可以产生1-7的随机数求rand13要求是均等)
5. [各类排序算法](./algorithm.md#5-各类排序算法)
6. [二叉树](./algorithm.md#6-二叉树)

-----

### 1. 递归计算1到100的和

```swift
func makeSum(_ num: NSInteger) -> NSInteger {
    
    if num > 0 {
        
        i = i + 1
        return makeSum(num - 1) + num
        
    }
    return 0
}

makeSum(100)
```

### 2. 设计一个递归搜索制定目录下指定类型文件到函数，要求写清返回值、函数名、参数列表。*只写函数名，不用写函数内容

伪代码：

```
func isSomeFile(filePath: String) -> BOOL {} // 判断是否是某种类型文件
func isDirectory(filePath: String) -> BOOL {} // 判断是否是文件夹
func findSomeFile(filePath: String) {
    if isDirectory(filePath) { // 如果是文件夹路径
        for subPath in filePath.subPath {
            findSomeFile(subPath) // 递归调用
        }
    }else { 
        // 不是文件夹路径，则打印出文件路径
        if isSomeFile(filePath) {
            print(filePath)
        }
    }
}
```

swift 实现：

```swift
func isSomeFile(_ fileName: String) -> Bool {
    
    return true
}

func findSomeFile(_ filePath: String) {
    
    let manager = FileManager.default
    var isDir = ObjCBool(false)
    let isExist = manager.fileExists(atPath: filePath, isDirectory: &isDir)
    
    if isExist {
        if isDir.boolValue {
            
            guard let dirArray = try?  manager.contentsOfDirectory(atPath: filePath) else { return }
            var subPath: URL
            for str in dirArray {
                subPath = URL(fileURLWithPath: filePath).appendingPathComponent(str)
                var isSubDir = ObjCBool(false)
                manager.fileExists(atPath: subPath.absoluteString, isDirectory: &isSubDir)
                findSomeFile(subPath.absoluteString)
            }
        }else {
            
            let fileName = URL(fileURLWithPath: filePath).lastPathComponent
            if isSomeFile(fileName) {
                print(fileName)
            }
        }
    }
}
```

### 3. n个数不相等，求其中的max、min，考虑比较次数，最优的比较次数是多少？

分治处理：

同时找到最大值和最小值的话，一般方法（遍历两遍）是O（2n），改进方法是每次读两个数，这两个数比一次，然后大的和当前最大值比，小的和当前最小值比。这样每两个数比了三次，故复杂度是O（3/2n），不过两种方法都是O（n）。算法导论里面有介绍。

参考： https://www.zhihu.com/question/28892158

### 4. 利用rand7（可以产生1-7的随机数），求rand13（要求是均等）

分析：

要保证`rand13()`在整数`1-13`的均匀分布，可以构造一个`1-13*n`的均匀分布的随机整数区间（n为任何正整数）。假设x是这个`1-13*n`区间上的一个随机整数，那么`x%13+1`就是均匀分布在`1-13`区间上的整数。由于`(rand7()-1)*7+rand7()`可以构造出均匀分布在`1-49`的随机数（原因见下面的说明），可以将40～49这样的随机数剔除掉，得到的数1-39仍然是均匀分布在1-39的，这是因为每个数都可以看成一个独立事件。

下面说明为什么`(rand7()-1)*7+rand7()`可以构造出均匀分布在1-49的随机数:

首先`rand7()-1`得到一个离散整数集合`{0，1，2，3，4，5，6}`，其中每个整数的出现概率都是1/7。那么`(rand7()-1)*7`得到一个离散整数集合`A={0，7，14，21，28，35，42}`，其中每个整数的出现概率也都是1/7。而`rand7()`得到的集合`B={1，2，3，4，5，6，7}`中每个整数出现的概率也是1/7。显然集合A和B中任何两个元素组合可以与1-49之间的一个整数一一对应，也就是说1-49之间的任何一个数，可以唯一确定A和B中两个元素的一种组合方式，反过来也成立。由于A和B中元素可以看成是独立事件，根据独立事件的概率公式`P(AB)=P(A)P(B)`，得到每个组合的概率是`1/7*1/7=1/49`。因此`(rand7()-1)*7+rand7()`生成的整数均匀分布在1-49之间，每个数的概率都是1/49。

参考：https://blog.csdn.net/ljsspace/article/details/6820753

代码参考：swift

```swift
func rand7() -> NSInteger {
    
    return NSInteger(arc4random() % 7) + 1
}

func rand13() -> NSInteger {
    
    var x = 0
    
    repeat {
        x = (rand7() + 1) * 7 + rand7()
    }while x > 39;
    
    return x % 13 + 1;
}
```

### 5. 各类排序算法

a. 选择排序（Selection Sort）

```objective-c
- (void)selectionSort:(NSArray <NSNumber *>*)arr {
    
    NSLog(@"%s",__func__);
    NSMutableArray <NSNumber *>*mArr = arr.mutableCopy;
    NSInteger count = mArr.count;
    NSInteger minIndex = 0;
    for (NSInteger i = 0; i < count - 1; i ++) {
        
        minIndex = i;
        for (NSInteger j = i + 1; j < count; j ++) {
            if (mArr[j].integerValue < mArr[minIndex].integerValue) {
                minIndex = j;
            }
        }
        [mArr exchangeObjectAtIndex:i withObjectAtIndex:minIndex];
    }
    NSLog(@"%@",mArr.copy);
}
```

b. 冒泡排序（Bubble Sort）

```objective-c
- (void)bubbleSort:(NSArray <NSNumber *>*)arr {

    NSMutableArray <NSNumber *>*mArr = arr.mutableCopy;
    NSInteger count = mArr.count;
    for (NSInteger i = 0; i < count - 1; i ++) {

        for (NSInteger j = 0; j < count - i - 1; j ++) {

            if (mArr[j].integerValue > mArr[j + 1].integerValue) {

                [mArr exchangeObjectAtIndex:j withObjectAtIndex:j + 1];
            }
        }
    }
    NSLog(@"%@",mArr.copy);
}
```

c. 插入排序（Insertion Sort）

```objective-c
- (void)insertionSort:(NSArray <NSNumber *>*)arr {
    
    NSMutableArray <NSNumber *>*mArr = arr.mutableCopy;
    NSInteger count = mArr.count;
    NSInteger preIndex = 0, current = 0;
    for (NSInteger i = 1; i < count; i ++) {
        
        preIndex = i - 1;
        current = mArr[i].integerValue;
        while (preIndex >= 0 && mArr[preIndex].integerValue > current) {
            
            mArr[preIndex + 1] = mArr[preIndex];
            preIndex --;
        }
        mArr[preIndex + 1] = @(current);
    }
    NSLog(@"%@",mArr.copy);
}
```

d. 归并排序（Merge Sort）

```objective-c
- (NSArray *)mergeSort:(NSArray *)arr {
    
    NSInteger count = arr.count;
    if (count < 2) {
        return arr;
    }
    NSInteger middle = floor(count / 2);
    NSArray *left = [arr subarrayWithRange:NSMakeRange(0, middle)];
    NSArray *right = [arr subarrayWithRange:NSMakeRange(middle, count - middle)];
    NSArray *result = [self mergeWithLeft:[self mergeSort:left] right:[self mergeSort:right]];
    
    return result;
}

- (NSArray *)mergeWithLeft:(NSArray <NSNumber *>*)leftArr right:(NSArray <NSNumber *>*)rightArr {
    
    NSMutableArray *result = @[].mutableCopy;
    NSMutableArray <NSNumber *>*left = leftArr.mutableCopy;
    NSMutableArray <NSNumber *>*right = rightArr.mutableCopy;
    
    while (left.count > 0 && right.count > 0) {
        
        if (left.firstObject.integerValue <= right.firstObject.integerValue) {
            
            [result addObject:left.firstObject];
            [left removeObjectAtIndex:0];
        }else {
            
            [result addObject:right.firstObject];
            [right removeObjectAtIndex:0];
        }
    }
    while (left.count) {
        
        [result addObject:left.firstObject];
        [left removeObjectAtIndex:0];
    }
    while (right.count) {
        
        [result addObject:right.firstObject];
        [right removeObjectAtIndex:0];
    }
    return result.copy;
}
```

e. 快速排序（Quick Sort）

```objective-c
- (NSArray *)quickSort:(NSMutableArray *)arr
        leftIndex:(NSInteger)leftIndex
       rightIndex:(NSInteger)rightIndex {
    
    if (leftIndex < rightIndex) {
        
        NSInteger temp = [self getMiddleIndex:arr leftIndex:leftIndex rightIndex:rightIndex];
        [self quickSort:arr leftIndex:leftIndex rightIndex:temp - 1];
        [self quickSort:arr leftIndex:temp + 1 rightIndex:rightIndex];
    }
    return arr.copy;
}

- (NSInteger)getMiddleIndex:(NSMutableArray *)arr
                  leftIndex:(NSInteger)left
                 rightIndex:(NSInteger)right {
    
    NSInteger tempValue = [arr[left] integerValue];
    while (left < right) {
        // 首先从右边开始查找比基准数小的值
        while (left < right && tempValue <= [arr[right] integerValue]) {
            // 如果比基准数大，继续查找
            right --;
        }
        if (left < right) {
            // 如果比基准数小，则将查找到的小值调换到i的位置
            arr[left] = arr[right];
        }
        // 当在右边查找到一个比基准数小的值时，就从i开始往后找比基准数大的值
        while (left < right && [arr[left] integerValue] <= tempValue) {
            // 如果比基准数小，继续查找
            left ++;
        }
        if (left < right) {
            // 如果比基准数大，则将查找到的大值调换到j的位置
            arr[right] = arr[left];
     }
    }
    // 将基准数放到正确位置
    arr[left] = @(tempValue);
    return left;
}
```

### 6. 二叉树

BinaryTreeNode.h 文件：

```
@interface BinaryTreeNode : NSObject

@property (nonatomic, assign) NSInteger value;

@property (nonatomic, strong) BinaryTreeNode *leftNode;

@property (nonatomic, strong) BinaryTreeNode *rightNode;

@end
```

BinaryTreeNode.m 文件：

```
// 创建二叉排序树
+ (BinaryTreeNode *)createTreeWithValues:(NSArray <NSNumber *>*)values {
    
    BinaryTreeNode *root = nil;
    for (NSInteger i = 0; i < values.count; i ++) {
        
        NSInteger value = values[i].integerValue;
        root = [self addTreeNode:root value:value];
    }
    return root;
}

// 添加节点
+ (BinaryTreeNode *)addTreeNode:(BinaryTreeNode *)treeNode value:(NSInteger)value {
    
    if (!treeNode) {
        
        treeNode = [BinaryTreeNode new];
        treeNode.value = value;
    }else if (value < treeNode.value) {
        
        treeNode.leftNode = [self addTreeNode:treeNode.leftNode value:value];
    }else {
        
        treeNode.rightNode = [self addTreeNode:treeNode.rightNode value:value];
    }
    return treeNode;
}

// 翻转 - 递归
+ (BinaryTreeNode *)invertBinaryTree:(BinaryTreeNode *)rootNode {
    
    if (!rootNode) {
        return nil;
    }
    if (!rootNode.leftNode && rootNode.rightNode) {
        
        return rootNode;
    }
    [BinaryTreeNode invertBinaryTree:rootNode.leftNode];
    [BinaryTreeNode invertBinaryTree:rootNode.rightNode];
    BinaryTreeNode *tempNode = rootNode.leftNode;
    rootNode.leftNode = rootNode.rightNode;
    rootNode.rightNode = tempNode;
    
    return rootNode;
}

// 翻转 - 非递归
+ (BinaryTreeNode *)invertBinaryTreeNot:(BinaryTreeNode *)rootNode {
    
    if (!rootNode) {
        return nil;
    }
    if (!rootNode.leftNode && !rootNode.rightNode) {
        return rootNode;
    }
    NSMutableArray *queueArray = [NSMutableArray new];
    [queueArray addObject:rootNode];
    while (queueArray.count > 0) {
        
        BinaryTreeNode *node = queueArray.firstObject;
        [queueArray removeObjectAtIndex:0];
        
        BinaryTreeNode *pLeft = node.leftNode;
        node.leftNode = node.rightNode;
        node.rightNode = pLeft;
        if (node.leftNode) {
            [queueArray addObject:node.leftNode];
        }
        if (node.rightNode) {
            [queueArray addObject:node.rightNode];
        }
    }
    return rootNode;
}


/**
 *  二叉树中某个位置的节点（按层次遍历）
 */
+ (BinaryTreeNode *)treeNodeAtIndex:(NSInteger)index inTree:(BinaryTreeNode *)rootNode {
    
    if (!rootNode || index < 0) {
        return nil;
    }
    // 数组当成队列
    NSMutableArray *queueArray = [NSMutableArray new];
    // 压入根节点
    [queueArray addObject:rootNode];
    while (queueArray.count > 0) {
        BinaryTreeNode *node = [queueArray firstObject];
        if (index == 0) {
            return node;
        }
        [queueArray removeObjectAtIndex:0];
        // 移除节点，index减少
        index --;
        if (node.leftNode) {
            [queueArray addObject:node.leftNode];
        }
        if (node.rightNode) {
            [queueArray addObject:node.rightNode];
        }
    }
    // 遍历完没有找到位置，返回nil
    return nil;
}

/**
 *  先序遍历：先访问根，再遍历左子树，再遍历右子树。典型的递归思想
 */
+ (void)preOrderTraverseTree:(BinaryTreeNode *)rootNode handler:(void(^)(BinaryTreeNode *treeNode))handler {
    
    if (rootNode) {
        
        if (handler) {
            handler(rootNode);
        }
        [BinaryTreeNode preOrderTraverseTree:rootNode.leftNode handler:handler];
        [BinaryTreeNode preOrderTraverseTree:rootNode.rightNode handler:handler];
    }
}

/**
 *  中序遍历：先遍历左子树，再访问根，再遍历右子树
 */
+ (void)inOrderTraverseTree:(BinaryTreeNode *)rootNode handler:(void(^)(BinaryTreeNode *rootNOde))handler {
    
    if (rootNode) {
        
        [BinaryTreeNode inOrderTraverseTree:rootNode.leftNode handler:handler];
        if (handler) {
            handler(rootNode);
        }
        [BinaryTreeNode inOrderTraverseTree:rootNode.rightNode handler:handler];
    }
}

/**
 *  后序遍历：先遍历左子树，再遍历右子树，再访问根
 */
+ (void)postOrderTraverseTree:(BinaryTreeNode *)rootNode handler:(void(^)(BinaryTreeNode *node))handler {
    
    if (rootNode) {
        
        [BinaryTreeNode postOrderTraverseTree:rootNode.leftNode handler:handler];
        [BinaryTreeNode postOrderTraverseTree:rootNode.rightNode handler:handler];
        if (handler) {
            handler(rootNode);
        }
    }
}

/**
 *  层次遍历（广度优先）
 */
+ (void)levelTraverseTree:(BinaryTreeNode *)rootNode handler:(void(^)(BinaryTreeNode *node))handler {
    
    if (!rootNode) {
        return;
    }
    NSMutableArray *queueArray = [NSMutableArray new]; // 数组当成队列
    [queueArray addObject:rootNode];
    while (queueArray.count > 0) {
        BinaryTreeNode *node = [queueArray firstObject];
        if (handler) {
            handler(node);
        }
        [queueArray removeObjectAtIndex:0]; // 弹出最前面的点
        if (node.leftNode) {
            [queueArray addObject:node.leftNode];
        }
        if (node.rightNode) {
            [queueArray addObject:node.rightNode];
        }
    }
}

/**
 *  二叉树的深度
 */
+ (NSInteger)depthOfTree:(BinaryTreeNode *)rootNode {
    
    if (!rootNode) {
        return 0;
    }
    if (!rootNode.leftNode && !rootNode.rightNode) {
        return 1;
    }
    NSInteger leftDepth = [BinaryTreeNode depthOfTree:rootNode.leftNode];
    NSInteger rightDepth = [BinaryTreeNode depthOfTree:rootNode.rightNode];
    
    return MAX(leftDepth, rightDepth) + 1;
}

/**
 *  二叉树的宽度
 */
+ (NSInteger)widthOfTree:(BinaryTreeNode *)rootNode {
    
    if (!rootNode) {
        return 0;
    }
    NSMutableArray *queueArray = [NSMutableArray new];
    [queueArray addObject:rootNode];
    NSInteger maxWidth = 1; // 最大的宽度，初始化为1，因为已有根节点
    NSInteger curWidth = 0; // 当前层的宽度
    
    while (queueArray.count > 0) {
        
        curWidth = queueArray.count;
        // 依次弹出当前层的节点
        for (NSInteger i = 0; i < curWidth; i ++) {
            
            BinaryTreeNode *node = [queueArray firstObject];
            [queueArray removeObjectAtIndex:0];
            if (node.leftNode) {
                [queueArray addObject:node.leftNode];
            }
            if (node.rightNode) {
                [queueArray addObject:node.rightNode];
            }
        }
        maxWidth = MAX(maxWidth, queueArray.count);
    }
    return maxWidth;
}

/**
 *  二叉树的所有节点数
 */
+ (NSInteger)numberOfNodesInTree:(BinaryTreeNode *)rootNode {
    
    if (!rootNode) {
        return 0;
    }
    NSInteger leftCount = [BinaryTreeNode numberOfNodesInTree:rootNode.leftNode];
    NSInteger rightCount = [BinaryTreeNode numberOfNodesInTree:rootNode.rightNode];
    
    return leftCount + rightCount + 1;
}

/**
 *  二叉树某层中的节点数
 */
+ (NSInteger)numberOfNodesOnLevel:(NSInteger)level inTree:(BinaryTreeNode *)rootNode {
    
    if (!rootNode || level < 1) {
        return 0;
    }
    if (level == 1) {
        return 1;
    }
    // 递归：level层节点数 = 左子树level-1层节点数+右子树level-1层节点数
    NSInteger leftCount = [BinaryTreeNode numberOfNodesOnLevel:level-1 inTree:rootNode.leftNode];
    NSInteger rightCount = [BinaryTreeNode numberOfNodesOnLevel:level-1 inTree:rootNode.rightNode];
    return  leftCount + rightCount;
}

/**
 *  二叉树叶子节点数
 */
+ (NSInteger)numberOfLeafsInTree:(BinaryTreeNode *)rootNode {
    if (!rootNode) {
        return 0;
    }
    // 左子树和右子树都是空，说明是叶子节点
    if (!rootNode.leftNode && !rootNode.rightNode) {
        return 1;
    }
    return [BinaryTreeNode numberOfLeafsInTree:rootNode.leftNode] + [BinaryTreeNode numberOfLeafsInTree:rootNode.rightNode];
}

/**
 *  二叉树的最大距离（直径）
 */
+ (NSInteger)maxDistanceOfTree:(BinaryTreeNode *)rootNode {
    if (!rootNode) {
        return 0;
    }
    //  方案一：（递归次数较多，效率较低）
    // 分3种情况：
    //1、最远距离经过根节点：距离 = 左子树深度 + 右子树深度
    NSInteger distance = [BinaryTreeNode depthOfTree:rootNode.leftNode] + [BinaryTreeNode depthOfTree:rootNode.rightNode];
    //2、最远距离在根节点左子树上，即计算左子树最远距离
    NSInteger disLeft = [BinaryTreeNode maxDistanceOfTree:rootNode.leftNode];
    //3、最远距离在根节点右子树上，即计算右子树最远距离
    NSInteger disRight = [BinaryTreeNode maxDistanceOfTree:rootNode.rightNode];
    
    return MAX(MAX(disLeft, disRight), distance);
}
```

参考：[二叉树的各种问题]( https://www.jianshu.com/p/a270d117e116)





