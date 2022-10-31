---
title: 'C#中的async/await'
status: 'published'
author:
  name: 'CrayonLSnail'
  picture: 'https://avatars.githubusercontent.com/u/30032078?v=4'
slug: 'c-async-await'
description: 'Async、Await是net4.5新增的异步编程方式，其目的是为了简化异步程序编写。所以使用时请注意net版本最低为4.5
'
coverImage: '/images/snipaste_2022-10-31_18-23-48-Q1NT.jpg'
publishedAt: '2019-01-18T02:23:00.000Z'
---

我们先做一个实验

### Test 1.

```C#
class Program
    {
        static void Main(string[] args)
        {
            Testasync();
            Console.WriteLine("查看异步");
            Console.ReadKey();
        }
        static async void Testasync()
        {
            for (int i = 0; i < 5; i++)
            {
                await My();
                Console.WriteLine("异步执行" + i.ToString() + "..");
            }
        }
        static async Task<int> My()
        {
            await Task.Delay(1000);
            Console.WriteLine("异步内部执行");
            return 0;
        }
    }
```

执行结果为

![UTOOLS1547780068637.png](https://i.loli.net/2019/01/18/5c413fe57e2fc.png)

现在我们来分析一下：

官方资料解释说：

> 使用 `async` 修饰符可将方法、[lambda 表达式](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)或[匿名方法](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/statements-expressions-operators/anonymous-methods)指定为异步。 如果对方法或表达式使用此修饰符，则其称为异步方法。
> 
> 异步方法同步运行，直至到达其第一个 `await` 表达式，此时会将方法挂起，直到等待的任务完成。
> 
> 如果 `async` 关键字修改的方法不包含 `await` 表达式或语句，则该方法将同步执行。 编译器警告将通知你不包含 `await` 语句的任何异步方法，因为该情况可能表示存在错误。
> 
> `async` 关键字是上下文关键字，原因在于只有当它修饰方法、lambda 表达式或匿名方法时，它才是关键字。 在所有其他上下文中，都会将其解释为标识符。

所以简单来说，在调用有`async`修饰方法时是异步执行的，但当方法内部遇到await则会等待执行完成。

当我们的`Main`方法调用Testasync时，Main方法和Testasync方法同时执行。

这时异步方法内部遇到了第一个`await&nbsp;`延迟1秒，

所以控制台输出“查看异步”

等待过后由`My`方法输出"异步内部执行"

再由`Testasync`输出"异步执行0.."

### Test 2.

现在我们取消`Testasync`方法中的`await`

代码

```C#
class Program
    {
        static void Main(string[] args)
        {
            Testasync();
            Console.WriteLine("查看异步");
            Console.ReadKey();
        }
        static async void Testasync()
        {
            for (int i = 0; i < 5; i++)
            {
                My();
                Console.WriteLine("异步执行" + i.ToString() + "..");
            }
        }
        static async Task<int> My()
        {
            await Task.Delay(1000);
            Console.WriteLine("异步内部执行");
            return 0;
        }
    }
```

执行结果

![UTOOLS1547780104090.png](https://i.loli.net/2019/01/18/5c414008ed76e.png)

继续分析

`Main`方法调用`Testasync`方法内部循环输出"异步执行{i}.."

`Testasync`方法调用`My`方法，但被方法中的`await Task.Delay(1000);`阻塞等待

`Main`方法输出"查看异步"

`Testasync`方法等待完成输出"异步内部执行"

<br>

