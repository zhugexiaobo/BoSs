---
title: NSArray 使用 
layout: post
tags: iOS 
---

```
// 创建有多个元素的数组，nil代表数组元素结束
NSMutableString *s1 = [NSMutableString stringWithString:@"a"];
NSMutableString *s2 = [NSMutableString stringWithString:@"b"];
NSMutableString *s3 = [NSMutableString stringWithString:@"c"];
NSArray *arr1 = [NSArray arrayWithObjects:s1, s2, s3, nil];
// 获取数组元素个数
NSLog(@"%zi", [arr1 count]); //3
// 判断数组中是否包含某个元素
BOOL r1 = [arr1 containsObject:@"b"];
NSLog(@"%i", r1); //1
// 获取最后一个元素
NSLog(@"%@", [arr1 lastObject]); //c
// 获取第一个元素 iOS 7+
NSLog(@"%@", [arr1 firstObject]); //a
// 通过索引获取数组元素
NSLog(@"%@", [arr1 objectAtIndex:1]); //b
// 查找元素的索引
NSLog(@"%zi", [arr1 indexOfObject:@"c"]); //2
// 让数组中的每个元素都调用setString:方法
[arr1 makeObjectsPerformSelector:@selector(setString:) withObject:@"d"];
NSLog(@"%@", arr1); //(d,d,d)
// 使用for遍历数组
NSUInteger count = [arr1 count];
for (int i=0; i<count; i++) {
    NSLog(@"%@", [arr1 objectAtIndex:i]);
}
// 使用for in遍历数组
for (id obj in arr1) {
    NSLog(@"%@", obj);
}
// 使用Block遍历数组
[arr1 enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
    NSLog(@"%zi = %@", idx, obj);
    if (idx==1) *stop = YES; //遍历到第2个元素终止遍历
}];
// 通过迭代器遍历数组 正序
NSEnumerator *et1 = [arr1 objectEnumerator];
id o1 = nil;
while (o1 = [et1 nextObject]) {
    NSLog(@"%@", o1);
}
// 通过迭代器遍历数组 反序
NSEnumerator *et2 = [arr1 reverseObjectEnumerator];
id o2 = nil;
while (o2 = [et2 nextObject]) {
    NSLog(@"%@", o2);
}


// 派生出新的数组
NSArray *array1 = [NSArray arrayWithObjects:@"1", @"2", nil];
NSArray *array2 = [array1 arrayByAddingObject:@"3"];
NSArray *array3 = [array1 arrayByAddingObjectsFromArray:[NSArray arrayWithObjects:@"4", @"5", nil]];
NSLog(@"%@", array1); // (1,2)
NSLog(@"%@", array2); // (1,2,3)
NSLog(@"%@", array3); // (1,2,4,5)
NSArray *array4 = [array3 subarrayWithRange:NSMakeRange(1, 2)];
NSLog(@"%@", array4); // (2,4)

// 利用分隔符将数组元素拼接成一个字符串
NSString *str = [array3 componentsJoinedByString:@"-"]; //1-2-4-5
NSLog(@"%@", str);

// 将数组写入文件（xml格式），生成的文件见下图
[array3 writeToFile:@"/Users/Lane/Desktop/array.xml" atomically:YES];
// 从文件中读取数组，格式见下图
NSArray *array5 = [NSArray arrayWithContentsOfFile:@"/Users/Lane/Desktop/array.xml"];
NSLog(@"%@", array5);

// 通过selector进行数组排序
NSArray *array6 = [NSArray arrayWithObjects:@"4", @"2", @"5", @"3", nil];
// 指定元素的比较方法：NSString的compare:方法
NSArray *array7 = [array6 sortedArrayUsingSelector:@selector(compare:)];
NSLog(@"%@", array7); // (2,3,4,5)
// 如果是其他类型，则要自定义比较方法
// 例如Student有fistName和lastName两个成员变量，则比较方法如下：
/*
    - (NSComparisonResult)compare:(Student *)otherStudent {
    // 先比较fistName
    NSComparisonResult result = [self.fistName compare:otherStudent.fistName];
    if (result==NSOrderedSame) { // 如果fistName相同，则比较lastName
        result = [self.lastName compare:otherStudent.lastName];
    }
    return result;
    }
*/
// 通过Block进行数组排序
NSArray *array8 = [array6 sortedArrayUsingComparator:^NSComparisonResult(NSString *obj1, NSString *obj2) {
    return [obj1 compare:obj2];
}];
NSLog(@"%@", array8); // (2,3,4,5)
// 通过排序描述器进行数组排序
/*
    NSSortDescriptor *bookNameDesc = [NSSortDescriptor sortDescriptorWithKey:@"book.name" ascending:YES];
    NSSortDescriptor *fistNameDesc = [NSSortDescriptor sortDescriptorWithKey:@"fistName" ascending:YES];
    NSSortDescriptor *lastNameDesc = [NSSortDescriptor sortDescriptorWithKey:@"lastName" ascending:YES];
    // 先按书名排序，再按fistName排序，最后按lastName排序
    [array sortedArrayUsingDescriptors:[NSArray arrayWithObjects:bookNameDesc, fistNameDesc, lastNameDesc, nil]];
*/

}
```