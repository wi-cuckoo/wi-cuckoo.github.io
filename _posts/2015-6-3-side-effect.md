---
title: Side Effect
---

>    
    int a = 0;
    a = a++;
    
So, what the value of **a** ?

### What's the Side Effect

Accessing a volatile object, modifying an object, modifying a file, or calling a function that 
does any of thoseoperations are all side effects, which are changes in the state of the execution
environment.
                                                                       
———— 《ISO/IEC 9899:201x》
    
主要的点在于那个 changes。假设你有一条表达式 int num = 0; ，那么声明变量 num 就是一个 side effect，然后将 num 赋值
0 也是一个 side effect，因为这些都是程序执行环境声明的变化（没找到更好的表述），在这条语句执行完后，我们都知道 num 
的值为 0 了。那么这里还有另外一个概念： Sequence Point。 

### What's the Sequence Point

The following are the sequence points described in 5.1.2.3:

* Between the evaluations of the function designator and actual arguments in a function call and the actual call.
  (6.5.2.2).
* Between the evaluations of the first and second operands of the following operators: logical AND && (6.5.13);
  logical OR || (6.5.14); comma , (6.5.17).
* Between the evaluations of the first operand of the conditional ? : operator and whichever of the second and 
  third operands is evaluated (6.5.15).
* The end of a full declarator: declarators (6.7.6);
* Between the evaluation of a full expression and the next full expression to be evaluated. The following are 
  full expressions: an initializer that is not part of a compound literal (7.7.9); the expression in an expression
  statement (6.8.3); the controlling expression of a selection statement (if or switch) (6.8.4); the controlling 
  expression of a while or do statement (6.8.5); each of the (optional) expressions of a for statement (6.8.5.3); 
  the (optional) expression in a return statement (6.8.6.4).
* Immediately before a library function returns (7.1.4).
* After the actions associated with each formatted input/output function conversion specifier (7.21.6, 7.29.2).
* Immediately before and immediately after each call to a comparison function, and also between any call to a
  comparison function and any movement of the objects passed as arguments to that call (7.22.5).

———— 《ISO/IEC 9899:201x》
    
如果能全部理解最好不过了，但是平时都用不到这么牛角尖的玩意，所以我还是来举个例子好了，就之前那个——作为一个很常规的
表达式，它的末尾就是一个 Sequence Point。又如调用 pirntf 函数结束后也有一个 Sequence Point。并且要记住，当达到一个 
Sequence Point 时，之前的 Side Effect 都已经作用完毕了。这很好理解，还是那个给 num 赋值 0 的情况，赋值完后，就遇到了
一个 Point，那么后面的代码执行中，不会再无缘无故又给 num 赋值 0 了。

### Example Analysis

回到我们开始那个问题：

    	
    	int i = 0;
    	
    	i = i++;

换我以前直接就判断 i 结果为 0，因为 i++ 是先用后加嘛，但是马上问题就来了，那加 1 后值也改变了啊，怎么感觉矛盾了呢？
有人马上指出，这里 ++ 的操作优先级比 = 高，所以先加，那么 i 就该是等于 1 了。嗯，似乎没什么问题（其实有漏洞）。但看了
上面的内容后知道了，这一条语句有两个 Side Effect，而语句结束后，这两个 Side Effect 都必须作用完。所以问题又来了，是赋
值先发生呢，还是 ++ 先发生呢，这个可不一定了，但这条语句完后肯定都发生了，因此为 0，为 1 都有可能。设计代码时，在两个
Sequence Point 之间同一个变量最多只能改变一次（这是保证代码执行结果确定的必要条件）。
