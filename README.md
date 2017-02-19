# Block-基础
block在iOS 4.0之后，它本身封装了一段代码并将这段代码当做变量，通过block()的方式进行回调。
## block与c语言函数的对比
### cc语言函数定义和调用
```objc
int sum(int a, int b){
    // code
    int sum = a + b;
    return sum;
}
int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // insert code here...
        int (*pSum)(int, int) = sum;
        int sum1 = (*pSum)(3, 4);
        NSLog(@"sum=%d", sum1);
    }
    return 0;
}
```
上面的函数指针可以直接通过(*pSum)(3, 4)的方式调用sum(int a, int b)这个函数，这样对比block跟似乎C语言的函数指针是一样的，但是两者仍然存在以下区别：
 1.block的代码是内联的，效率高于函数调用
 2.block对于外部变量默认是只读属性
 3.block被Objective-C看成是对象处理
## Block-声明和实现及要注意的问题
```objc
int a = 3;
    __block int b = 4;
    NSString * myStr = @"人生如梦";
    // block声明
    int (^sumBloc)(int x, int y);
    // block实现
    sumBloc = ^(int x, int y){
//        a = a + 1;    // block中修改外部变量，需要将外部变量外加上__block
        b = b + 1;      // block中可以访问外部变量
        return x + y;
    };
    // block的声明加实现
    NSString * (^myBlock)(NSString * str) = ^(NSString * str){
        str = [str stringByAppendingString:@",早生华发"];
        return str;
    };
    int sum = sumBloc(a,b);
    NSString * str = myBlock(myStr);
    NSLog(@"a=%d,b=%d;sum=%d,myStr=%@,str=%@", a, b, sum, myStr, str);
```
## 声明block属性的两种方法
```objc
#import <UIKit/UIKit.h>

typedef void (^sendValuBlock)(NSString * myStr);

@interface ViewBlockControllerViewController : UIViewController
/**
 *  直接声明一个block属性
 */
@property (nonatomic,copy) void (^sendBlock)(NSString * myStr);
/**
 *  结合typedef声明一个block属性
 */
@property (nonatomic,copy) sendValuBlock sendValueBlock;
/**
 *  需要传送的字符串
 */
@property (copy, nonatomic) NSString * sendStr;
@end
```
## 调用block：一般在响应事件触发时调用
```objc
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event
{
    if (self.sendBlock) self.sendBlock(self.sendStr);
    if (self.sendValueBlock) self.sendValueBlock(self.sendStr);
    [self dismissViewControllerAnimated:YES completion:nil];
}
```
## 外部类定义block代码块：类的外部定义block代码块
```objc
-(void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event
{
    ViewBlockControllerViewController * blockView = [ViewBlockControllerViewController new];
    blockView.view.backgroundColor = [UIColor greenColor];
    blockView.sendBlock = ^(NSString * str){
        NSLog(@"%@",str);
    };
    blockView.sendValueBlock = ^(NSString * str){
        NSLog(@"%@",str);
    };
    [self presentViewController:blockView animated:YES completion:nil];
}
```
