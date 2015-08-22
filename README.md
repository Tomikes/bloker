# bloker
块
1:块也属于oc对象，可以放入集合中。
2:快可以从闭包中取值。

基本语法：
//这个块没有返回值也不带参数，
^{
     NSLog(@"This is a block");
}

// 也可以用一个变量去标记一个块
void (^simpleBlock)(void);//这是一个块变量名字是simpleBlock。void就是不指定类型，前面是返回类型，后面是参数类型
//既然是变量那么不妨赋值
simpleBlock = ^{
    NSLog(@"This is a block");
};
//再进一步，将神明和复制合并
void (^simpleBlock)(void)  = ^{
                                   NSLog(@"This is a block");
                                };

//那么申明完成并赋值后如何使用呢？
simpleBlock();//像swfit中调用var属性一样。注意，如果你在app中调用一个nil的块，app将会crash

----------------------------------------------------------------------------------------------------
//接下来在做一些带参数的块

double (^multiplyTwoValues)(double, double) =   ^(double firstValue, double secondValue) {
                                                      return firstValue * secondValue;
                                                     };
                                                     
 double result =   multiplyTwoValues(2,4);   
 //是不是像swfit中
 var somevar(_,_)->_ {}.......
 var name = somevar(2,4);                                               
  
  --------------------------------------------------------------------------------------------------- 
 // 块捕获值, 在一个函数内部申明块，以及一些变量，块是可以获取块外面的值的
 - (void)testMethod {
    int anInteger = 42;
    void (^testBlock)(void) = ^{
        NSLog(@"Integer is: %i", anInteger);
};
    testBlock();
} //42
// 值得注意的是：

int anInteger = 42;
void (^testBlock)(void) = ^{
    NSLog(@"Integer is: %i", anInteger);
};
anInteger = 84;
testBlock();//42, 块是不能改变值的，先入为主
-------------------------------------------------------------------------------------------
//如果我想改变里面打印的值，怎么办？
__block int anInteger = 42;
void (^testBlock)(void) = ^{
    NSLog(@"Integer is: %i", anInteger);
};
anInteger = 84;
testBlock();//84 ,    在前面的变量上加_block
//但是在块内部可以直接修改
__block int anInteger = 42;
void (^testBlock)(void) = ^{
    NSLog(@"Integer is: %i", anInteger);
    anInteger = 100;
};
testBlock();
NSLog(@"Value of original variable is now: %i", anInteger);
//Integer is: 42
//Value of original variable is now: 100
---------------------------------------------------------------------------------------
//块作为参数处理回调
- (void)beginTaskWithCallbackBlock:(void (^)(void))callbackBlock;//申明，注意^符号后面的快名要提出来
- (void)beginTaskWithCallbackBlock:(void (^)(void))callbackBlock {
    ...
    callbackBlock();
}//-------------------------------- 实现部分
//带有参数的呢？
- (void)doSomethingWithBlock:(void (^)(double, double))block {
    ...
    block(21.0, 2.0);
}//值得注意的是块作为参数应该放在最后面
- (void)beginTaskWithName:(NSString *)name completion:(void(^)(void))callback;

//+++++++++++++++++++++++++++++++++++++++++++++快速定义+++++++++++++++++++++++++++++++++++++++++++++++++++++

typedef void (^mikeBlock)(void);
//然后在需要用的地方
mikeBlock oneBlock = ^{};//var
 - (void)beginFetchWithCallbackBlock:(mikeBlock)callbackBlock {
      ...
      callbackBlock();
};//func prama
//+++++++++++++++++++++++++++++++++++++++++++++块中块+++++++++++++++++++++++++++++++++
void (^(^complexBlock)(void (^)(void)))(void) = ^ (void (^aBlock)(void)) {
    ...
return ^{ ...
   }; 
};//返回一个块
//++++++++++++++++++++++++++++在属性中定义块++++++++++++++++++++++++++
@interface XYZObject : NSObject
@property (copy) void (^blockProperty)(void);
@end
//调用
方式1:self.blockProperty = ^{
    ...
};
方式2:self.blockProperty();
//+++++++++++++++++++++++++++避免强用循环+++++++++++++++++++++
@interface XYZBlockKeeper : NSObject
@property (copy) void (^block)(void);
@end

@implementation XYZBlockKeeper
  - (void)configureBlock {
    XYZBlockKeeper * __weak weakSelf = self;
    self.block = ^{
   [weakSelf doSomething];

       }
}
//++++++++++++++++++++++块权举，重点++++++++++++++++++++++
- (void)enumerateObjectsUsingBlock:(void (^)(id obj, NSUInteger idx, BOOL
*stop))block;

NSArray *array = ...
[array enumerateObjectsUsingBlock:^ (id obj, NSUInteger idx, BOOL *stop) {
    NSLog(@"Object at index %lu is %@", idx, obj);
    
      if (...) {
*stop = YES; }
}];
//并发权举
[array enumerateObjectsWithOptions:NSEnumerationConcurrent
                        usingBlock:^ (id obj, NSUInteger idx, BOOL *stop) {
... }];

    NSDictionary *dictionary = ...
    [dictionary enumerateKeysAndObjectsUsingBlock:^ (id key, id obj, BOOL *stop)
{
        NSLog(@"key: %@, value: %@", key, obj);
    }];

//+++++++++++++++++++++++++++++++++++++++不同队列增加快操作＋＋＋＋＋＋＋＋＋＋＋＋＋＋＋
//申明
NSBlockOperation *operation = [NSBlockOperation blockOperationWithBlock:^{
    ...
}];
//等待执行的
  // schedule task on main queue:
  NSOperationQueue *mainQueue = [NSOperationQueue mainQueue];
  [mainQueue addOperation:operation];

// schedule task on background queue:
  NSOperationQueue *queue = [[NSOperationQueue alloc] init];
  [queue addOperation:operation];

//＋＋＋＋＋＋＋＋＋＋＋＋＋＋＋＋＋＋多线程＋＋＋＋＋＋＋＋＋＋＋＋＋＋＋＋＋＋＋＋＋＋＋＋＋


dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    NSLog(@"Block for asynchronous execution");
});






 
