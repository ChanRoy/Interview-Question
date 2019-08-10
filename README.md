# Interview-Question
iOS 面试题积累

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

伪函数：

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

4. 利用rand7（可以产生1—7的随机数），求rand13（要求是均等）







