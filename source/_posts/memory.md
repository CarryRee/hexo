---
title: 浅谈block的循环引用
date: 2017-07-28 17:18:02
tags: 内存管理 block
---

浅谈block的循环引用
==


##### 1.前言：最近在项目上经常遇到控制器pop出来却不执行dealloc的，很多时候都是block循环引用导致，所以特意对block进行一番研究，以防自己犯引用错误；

##### 2.在ARC环境下，比较常见的发生循环引用就是self -> block -> self 从而形成环，导致不被释放，或者其他类持有block -> self，但这个没有释放，或没有主动释放block；

##### 3.案例分析：


控制器：

```
@interface BlockViewController ()

@property (nonatomic, strong) PropertyTest *propertyTest;
@property (nonatomic, strong) NSString *name;
@property (nonatomic, copy) void(^myBlock)();
@property (nonatomic, strong) NSMutableArray *dataArray;

@end
```

控制器属性：

```
@interface propertyTest : NSObject
@property (nonatomic, copy) void(^propertyBlock)();
@end
```

单例：

```
typedef void(^TestBlock)(void);

@interface Singleton : NSObject

@property (nonatomic, copy) TestBlock testBlock;

+ (instancetype) sharedSingleton;
- (void) test;
```
***

**1）GCD里面的block**

```
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
self.name = @"test";
});
```
结果：执行了dealloc <br>
分析：GCD是一次性派发的任务，当执行完block,会释放block，GCD本身也释放

**2) 属性的block**

```
self.propertyTest = [[PropertyTest alloc] init];

self.propertyTest.block = ^{
self.name = @"test";
};

```

结果：不执行了dealloc <br>
分析：self->self.propertyTest->block->self.name(self),此情况就是最典型的循环引用了，形成保留环无法释放

**3) 属性的block, 增加__weak声明**

```
self.propertyTest = [[PropertyTest alloc] init];

__weak typeof (self) wealSelf = self;
self.propertyTest.block = ^{
typeof(wealSelf) strongSelf = wealSelf;
strongSelf.name = @"test";
};

```

结果：执行了dealloc <br>
分析：self->self.propertyTest->block->self.name(self),但是weak打破了循环，后面那个strongSelf和原来的self并没有直接关系，因为strongSelf是通过weakSelf得来的，而weakSelf又没有强引用原来的self

**4) 成员的block**

```
self.propertyTest = [[PropertyTest alloc] init];

self.propertyTest.block = ^{
_name = @"test";
};

```

结果：不执行了dealloc <br>
分析：self->self.propertyTest->block->_name(self),跟2）上述同理

**5) 成员的block,增加__weak声明，但还是用成员**

```
self.propertyTest = [[PropertyTest alloc] init];

__weak typeof (self) weakSelf = self;
self.propertyTest.block = ^{
typeof(weakSelf) strongSelf = weakSelf;
_name = @"test";
};

```

结果：不执行了dealloc <br>
分析：self->self.propertyTest->block->_name(self),虽然被weak打破，还是用了self的成员name

**6) 属性的block,结束后重置为nil**

```
self.propertyTest.block = ^{
self.name = @"test";
self.propertyTest.block = nil;
self.propertyTest = nil;
NSLog(@"%@", self.propertyTest);
};

```

结果：不执行了dealloc <br>
分析：self->self.propertyTest->block->self.name(self),self.propertyTest = nil,self.propertyTest.block = nil;虽然强制为空，应该是拷贝到堆上的block还没有释放；

**7) 临时生成类**

```
PropertyTest *propertyTest = [[PropertyTest alloc] init];

propertyTest.block = ^{
self.name = @"test";
};

```

结果：执行了dealloc <br>
分析：propertyTest->block->self.name(self),未形成环,注意：在ARC中，在被拷贝的 block 中无论是直接引用self 还是通过引用self的成员变量间接引用self，该block都会retain self。但是block执行完会释放一次，临时生成的类释放也会在释放一次；

**8) 单例持有**

```
[Singleton sharedSingleton].testBlock = ^{
self.name = @"test";
};

```

结果：不执行了dealloc <br>
分析：Singleton 执行完释放一次block, 但是Singleton是单例, 并没有释放;

**9) 单例持有,__weak声明**

```
__weak typeof (self) weakSelf = self;
[Singleton sharedSingleton].testBlock = ^{
typeof(weakSelf) strongSelf = weakSelf;
strongSelf.name = @"test";
};
```

结果：执行了dealloc <br>
分析：__weak声明使该block不retain self,Singleton 执行完释放一次block,self也释放了；

**10) 单例持有，手动释放**

```
[Singleton sharedSingleton].testBlock = ^{
self.name = @"test";
};
[Singleton sharedSingleton].testBlock = nil;
```
结果：执行了dealloc <br>
分析：在ARC中，在被拷贝的 block 中无论是直接引用self 还是通过引用self的成员变量间接引用self，该block都会retain self。但是block执行完会释放一次，然后被手动释放了一次，self也释放了；


**11) 数组包含block**

```
PropertyTest *propertyTest = [[PropertyTest alloc] init];

propertyTest.block = ^{
self.name = @"test";
};

self.dataArray = [NSMutableArray array];
[self.dataArray addObject:propertyTest];
```
结果：不执行了dealloc <br>
分析：self.dataArray -> propertyTest -> block -> self.name ,这是间接持有


**12) 数组包含block, __weak声明**

```
PropertyTest *propertyTest = [[PropertyTest alloc] init];

__weak typeof (self) weakSelf = self;
propertyTest.block = ^{
typeof(weakSelf) strongSelf = weakSelf;
strongSelf.name = @"test";
};

self.dataArray = [NSMutableArray array];
[self.dataArray addObject:propertyTest];
```
结果：执行了dealloc <br>
分析：self.dataArray -> propertyTest -> block -> self.name ,这是间接持有, 但被 weak 打破循环

