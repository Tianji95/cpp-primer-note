虽然我们在写C++的大部分时候不会用到C++中的一些骚操作，但是作为一个科班毕业的程序员，如果去看别人的代码，看到这些骚操作的时候还是应该要看懂的。这也是为什么我要在网易实习了一年半以后重新捡起C++primer看一遍。查漏补缺。

**二、基本数据类型**

1. int 最小是16位的，意味着int在不同的编译器中即有可能是16位也有可能是32位，例如vs2017就是32位的
2. uint和int之间相互转换的时候要注意uint不能是负数，例如 unsigned int u = 10; int i = -42; cout<< u+i 就会出现4294967264.所以无符号变量尽量不要和有符号的变量混用。同理在float和int之间转换的时候，float转换成int也会丢失小数点后面的数字
3. 以U为后缀的字面值例如371U，将从与其能匹配的空间最小的一个作为其类型（从unsigned int, unsigned long unsigned long long）同理 后缀L也一样。
4. 初始化值的四种方法： int i = 0; int i = {0}; int i{0}; int i(0);不过花括号类型的列表初始化不能初始化两个不同类型的值，例如 long double i = 3.14; int j = {i}则会报错，而int j = i; 和 int j(i)则只会丢失精度。
5. extern 关键字：extern int i //声明但是不定义i，如果赋予初始值的话，则extern无用
6. 引用&必须在定义的时候给与初始值，指针不需要。因为引用是要和初始值一直绑定在一起的，无法指向其他的值。指针前面如果加上*const变成常量指针的话，也无法再改变了。
7. const 变量默认只在单个文件中共享，如果想要在多文件共享的时候，需要添加extern关键字
8. 引用的类型必须和其引用对象的类型一致，例外情况是：右边的表达式可以转换成左边的类型，例如 int i = 42; const int &r1 = i;是正确的，因为编译器在编译的时候回出现const int temp = i; const int &r1 = temp;这样的操作，所以const int &r2 = 42; int &r4 = r1 * 2不对，因为编译器无法转换。
9. 顶层（高等级const，top-level const）const 和底层const：顶层const表示指针本身是个常量，例如const int ci = 42. 底层const表示指针所指的对象是一个常量，例如const int *p2 = & ci;也会出现一个指针又是顶层又是底层const的情况，例如const int *const p3 = p2;其实就是指针const和普通const的区别，指针const（顶层const）是地址不能变，普通const是值不能变。
10. constexpr 关键字：由编译器来验证一个表达式是否是const表达式，如果指定是指针的话，默认是顶层指针，例如constexpr int *q = nullptr;q是一个常量指针。
11. typedef 的一个坑：如果我们定义typedef char *pstring; 这个时候const pstring指的就是一个指向char的常量指针（指向的地址不变）。而如果把他替换成原来的形式 const char *就变成了指向常量字符的指针，意思就变了。
12. auto 会自动忽略顶层const ，例如const int ci = i; auto f = ci;f 就会只是一个int，这个时候应该用 const auto f = ci;
13. decltype 关键字： 返回一个表达式里面的类型，例如：const int ci = 0; decltype(ci) x = 0; //x的类型是const int，另外有一个特殊的例子是int *p = &i ; decltype(*p) c则c是int&类型
14. 我们平时经常使用的#include， #define这些其实都有一个共同的名字：预处理器代码

**三、字符串、向量和数组**
1. 头文件中一般不使用using声明


