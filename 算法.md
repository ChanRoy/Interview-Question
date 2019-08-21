# Interview-Question
iOS 面试题积累 - 算法篇

1. 递归计算1到100的和	

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

2. 设计一个递归搜索制定目录下指定类型文件到函数，要求写清返回值、函数名、参数列表。*只写函数名，不用写函数内容。

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



3. n个数不相等，求其中的max、min，考虑比较次数，最优的比较次数是多少？

分治处理：

同时找到最大值和最小值的话，一般方法（遍历两遍）是O（2n），改进方法是每次读两个数，这两个数比一次，然后大的和当前最大值比，小的和当前最小值比。这样每两个数比了三次，故复杂度是O（3/2n），不过两种方法都是O（n）。算法导论里面有介绍。

参考： https://www.zhihu.com/question/28892158

4. 利用rand7（可以产生1-7的随机数），求rand13（要求是均等）

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

5. 各类排序算法：

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

6. 二叉树

   



