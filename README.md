
# 一、异常体系架构


## 1\.1 体系图解


![image](https://img2024.cnblogs.com/blog/2735096/202411/2735096-20241128163458889-781121430.png)


## 1\.2 Exception \& Error


### Exception


表示程序可以处理的异常情况，通常是由于程序逻辑错误或运行时问题引起的，比如NullPointException、IOException等。这些异常是设计用来被程序捕获，并采取相应的恢复措施来继续执行，即具有可恢复性。


### Error


表示系统级的错误，通常是是虚拟机无法恢复的错误，如OutOfMemoryError、StackOverflowError等。这些错误通常不期望程序能够处理，即不建议使用try...catch进行捕获处理，因为他们通常表明JVM本身遇到了严重问题，不具有可恢复性。


对于Error类及其子类的严重错误，通常由以下几个处理原则：


1. **不推荐捕获处理**：Error类及其子类通常表示程序无法继续正常运行的情况。捕获这些异常通常没有意义，因为它们表示的问题是程序无法控制和恢复的。例如，如果程序遇到了0ut0fMemoryError即使捕获了这个异常，程序也无法继续执行，因为内存已经耗尽。
2. **程序中止**：大多数Error类异常都是程序运行时的严重问题，它们通常会导致程序终止。捕获这些异常可能会导致程序处于一个不确定的状态，这可能比程序直接终止更危险。
3. **资源清理**：尽管不推荐捕获Error类异常，但在某些情况下，你可能想要在程序终止之前进行一些资源清理工作。在这种情况下，可以在catch块中添加资源释放的代码，但通常不会尝试恢复程序的正常执行。
4. **日志记录**：在捕获Error类异常时，一个常见的做法是记录详细的错误信息，这对于事后分析和调试是有帮助的。这可以通过在catch块中添加日志记录代码来实现。
5. **用户友好的反馈**：如果程序是面向用户的，那么在捕获Error类异常时，可以向用户提供一些友好的错误信息，而不是让程序突然崩溃，这样可以提高用户体验。
6. **程序设计**：在设计程序时，应该尽量避免可能导致Error类异常的情况。例如，通过合理的资源管理和错误处理来减少0ut0fMemoryError的发生。


### Simple Conclusion


总结来说，Exception是程序可以并且应该处理的异常，而Error是程序通常无法处理的严重错误。在实际编程中，我们通常会关注如何处理Exception，而对于Error，我们更多的是通过代码优化和资源管理来尽量避免它们的发生。


## 1\.3 Checked Exceptions \& UnChecked Exceptions


### Checked Exceptions


即检查型异常，指那些在编译时必须被处理的异常。它们通常是由于外部因素引起的，比如文件找不到、网络问题等。这类异常的存在有助于提高代码的健壮性，因为编译器会强制开发者处理这些潜在的错误情况——必须在编译时处理，要么通过try\-catch语句捕获处理，要么通过方法签名中的throws关键字声明抛出。Exception的子类（RuntimeException及其子类除外）都是检查型异常。


### Unchecked Exceptions


即非检查型异常，指那些在编译时不需要被处理的异常。它们通常是由于程序内部逻辑错误引起的比如数组越界、空指针引用等。这类异常通常表示程序中的错误，这些错误应该在开发阶段被修复，而不是在运行时通过异常处理机制来处理，因此编译器也并不会强制开发者捕获或声明抛出这些异常。RuntimeException及其子类属于非检查型异常，当然，Error及其子类也归属于非检查型异常。


### Simple Compare


![image](https://img2024.cnblogs.com/blog/2735096/202411/2735096-20241128164205047-314206514.png)


# 二、异常的声明、抛出与捕获


## 2\.1 声明异常


对于检查型异常，可以通过throws关键字声明，即在方法签名中声明，表示该方法可能会抛出异常。此时如果抛出了异常，那么方法调用者需要自己处理或继续声明和抛出。当你的方法无法处理某种异常，或者你希望调用者来处理这种异常时，你可以选择在方法签名中使用throws声明异常。


## 2\.2 抛出异常


抛出异常只会发生在方法内部，使用throw关键字抛出一个异常实例，由于异常实例是一个对象，所以抛出时都是 throw new xxxException 。当你的代码到达一个无法正常继续执行的状态，或者需要通知方法的调用者出现了一个异常情况时，就可以在代码中使用throw关键字来抛出一个异常。


## 2\.3 捕获处理异常


如果想要捕获并处理异常，是的程序以期望的步骤运行，就需要使用try\-catch进行异常的捕获。try代码块中放可能产生异常的代码，catch中进行异常的处理，可以选择抛出——即使用throw关键字，也可以选择记录异常日志，进行异常处理等。


## 2\.4 throws、throw、try\-catch、try\-catch\-finally、try\-finally


使用了throws声明异常后，方法代码块中可以不使用throw手动抛出异常，如果某句代码产生了声明的异常，会自己抛出。当然也可以手动抛出所声明的异常，常用于自定义异常。


使用了throws声明异常后，是否还需要try\-catch去捕获？如果在catch代码块中，并没有手动使用throw去抛出异常，那么这时候throws的声明是多余的。即方法调用者永远无法知道发生了什么异常，因为异常已经被捕获处理。


使用try\-catch的时候，如果catch中声明的异常类型（一个try可以多个catch）没有匹配到try块中产生的异常，异常仍然会向上传播。catch块中常常是处理异常，打印异常日志信息等，一般不做异常的抛出。如果做了异常的抛出，那我们希望要在方法签名上声明该异常。


使用try\-catch\-finally的时候，即使catch中没有捕获到发生的异常，finally中的代码块始终会被执行。


使用try\-finaly的时候，如果发生了异常，异常不会被捕获，会向上传播。


 本博客参考[wgetcloud全球加速服务机场](https://wa7.org)。转载请注明出处！
