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


1. 头文件（.h文件）中一般不使用using声明
2. 拷贝初始化和直接初始化： string s5 = "hiya"//拷贝初始化, string s6("hiyta")//直接初始化
3. 在执行读取操作时，string对象会忽略开头的空格，例如输入“   hello world”则第一个输入为hello
4. getline是一直读取直到出现换行符（换行符也读进来了）。
5. string.size()返回的是无符号整数
6. string 相加的时候必须保证+号两边至少有一个是string ，如 string s7 = "hello" + "," + s2;是不对的
7. vector初始化的几种方式： vector<string> a = {"1", "2", "3"}; vector<int> ivec(10, -1); vector<string> svec(10) //10个空的 vector<string> vec1(vec2); vector vec1 = vec2;
8. 但凡是使用了迭代器的循环体，都不要向迭代器所属的容器中添加元素（之前踩过这个坑）
9. char数组一定要考虑给空字符的位置，例如const char a4[6] = "daniel"; 是错误的，因为没有空间可以存放空字符
10. int * ptrs[10] //10个指针  int (*Parray)[10] = &arr ;// 一个指针，指向一个数组     int (&arrRef)[10] = arr; // 一个引用，引用一个数组   int *(&arry)[10] = ptrs; //arry是一个数组的引用，数组包含10个指针
11. int ia[] = {0,1,2,3,4}; auto ia2(ia); // 事实上是 auto ia2(&ia[0])所以ia2是一个整形指针
12. 数组不常用的begin和end   → → int *beg = begin(ia) //指向ia首元素的指针
13. 使用数组初始化vector ： vector<int> ivec(begin(int_arr), end(int_arr));

**四、表达式**


1. 位运算符号使用的时候经常会出现正负号符号的问题，且如何处理正负号由编译器决定，所以一般来说仅使用于无符号整型变量
2. 不同数据类型的转换：尽可能避免损失精度，例如int+double 会得到double， long+long long 会得到long long。另外，在if中，非bool转换成bool类型，在赋值中，右边值的类型转换成左边值的类型
3. 强制类型转换（尽量不要用)
static_cast : 可以进行任何具有明确定义的类型转换（除了const） 当需要把一个较大的算数类型赋值给较小的算数类型时很有用，此时会损失精度且不会有警告，也可以转换指针，例如 void *p = &d; double *dp = static_cast<double*>(p);
dynamic_cast  :运行时类型识别
const_cast ：只能改变运算对象的底层const    const char *pc; char *p = const_cast<char*>(pc);常常用于函数重载
reinterpret_cast（能不用就不用，太危险了。。。） ：通常为运算对象的位模式提供较低层次上的重新解释，例如 int *ip; char *pc = reinterpret_cast<char*>(ip);此时程序员必须牢记pc真实值是一个int而不是char，如果把pc当成char用会报错，例如string str(pc)会报错

**五、语句**


1. 使用空语句 例如 while(cin >> s && s!='a');最好写上注释注明这里是有意省略
2. 一般不要省略case后面的break，如果没有写break也最好加上注释说明程序的逻辑，同样最后一个标签的break虽然可以省略，但是还是建议不要省略，起码如果还要新增一个case 的话，就不用额外写break了。
3. 最好不要省略switch中的default，这样是为了告诉阅读代码的人，已经考虑到了default的情况。
4. switch 比较有意思的例子：

    case true:
        string file_name;
        int i = 0;
        int j;
        break;
    case false:
        j = next_num();
        if(file_name.empty())....
这个时候如果直接跳到false分支，则file_name会因为没有初始化出错，而file _name也在作用域内部.所以最好的方法还是自己用{}定义一个块
5. do while 语句可以先执行循环体，在有些情况可能会比较好用（这个平时用的有点少。。）
6. 异常处理代码：
    try{
        if(a == b){
            throw runtime_error("is not same");
        }
    }catch(runtime_error err){
        cout<<err.what();
    }
如果throw 出了一个异常，那么catch会按照函数调用关系依次向上寻找catch，如果最终没有找到catch，则会转到terminate的标准库函数中，程序非正常终止。<stdexcept>定义了出了runtime_error以外其他的几个异常类型，包括逻辑程序错误（logic _error），计算上溢（overflow _error）等


**六、函数**


1. static变量虽然也保存在程序的全局存储区当中，但是static只有其作用于可见，如果文件中的函数前添加static，则这个函数只有该文件可见。所以可以用于多文件多函数的定义
2. 函数用到引用的时候，如果不会改变其原来值的话，尽量使用常量引用，一方面可以告诉阅读者这个值不会改变，另一方面可以避免一些不必要的麻烦。例如下面将会报错：
    int find_char(string &s, char c);//错误定义
    int find_char(const string &s, char c);//正确定义
    find_char("hello world", "o");//调用
3. 函数中数组传递的三种方式
    void myfunc(const int*);
    void myfunc(const int[]);
    void myfunc(const int[10]);
    int i = 0; j[2] = {0, 1};
    myfunc(&i);
    myfunc(j);
4. func(int (&arr)[10])//arr是具有十个整数的整形数组的引用
   func(int &arr[10])//arr是引用的数组
5. main里面的(int argc, char *argv[])，程序本身的名字是argv[0],最后一个参数一定是argv[argc] = 0;
6. 可变参数的函数：所有实参类型相同：传递initializer_list这样一个标准库
                  void error_msg(initializer_list<string> msg);
                  error_msg({"123", str1, str2})//花括号不能去掉
                  所有实参类型不同：可变参数模板
                  省略符：void foo(int, ...),可以使用va_list， va_start va_end来取得参数的值
7. 函数可以返回左值，只不过比较奇怪，例如:
    char &get_val(){
        return str[idx]
    }
    get_val = 'A';
8. 函数也可以返回大括号包围值的列表，例如 vector<string> process(){return {"123", "4565"}}
9. 返回数组指针：C++无法直接返回数组指针，但是可以通过：
    typedef int arrT[10] 或者 using arrT = int[10]  →   arrT* func(int i);来实现
10. 函数的尾置返回类型->符号例如函数int (*func(int i))[10] 表示返回一个指针，该指针指向含有十个整数的数组，可以写成auto func(int i)->int(*)[10]    
11. C++中，名字查找发生在类型检查以前，所以在下面情况下，print("123")是无法找到print(string s)的，因为只能找到print(int i);

void print(string s);
void func(){
    void print(int i);
    print("123");
}

12. 一般inline函数用在较小的函数体上面，因为这些较小的函数体写出来的时候比等价的表达式可读性更高（有些操作写成函数的好处），或者想要在这些函数中加入一些debug信息，或者预期这个函数将来会变得很庞大。这些都是将这些操作写成内联小函数的好处
13. 常量表达式constexpr关键字：编译的时候将直接把函数转换成关键值，constexpr int new_sz(){return 42}   constexpr int foo = new_sz();constexpr函数内的语句在运行的时候不能执行操作。
14. 在编写自己的asset文件的时候，可以使用预处理器定义的宏，输出异常出现的文件行号等，例如下面如果在test.cpp调用myasset并且出现异常，就会输出test.cpp
"asset.h"
void myasset(){
    cout<< __FILE__<<endl;
} 
"test.cpp"
myasset();
15. 多个重载函数的实参类型转换顺序：1.实参形参类型相同、数组类型和指针类型之间的转换、添加或者删除顶层const。2.const转换实现的匹配。3.通过类型提升实现的匹配。4.算数类型转换或者指针转换实现的匹配。5.类类型转换实现的匹配
16. 函数指针：bool (*pf)(const string &, const string &); 含义：pf前面有一个*，所以pf是指针，右侧是形参列表，说明pf指向的是函数，函数返回是bool，说明pf指向一个返回值是bool的函数指针。
如果去掉括号，则bool *pf(const string &, const string &);是一个名为pf的函数，返回bool
17. 重载函数的指针：void ff(int *); void ff(unsigned int); 
                void (*pf1)(unsigned int) = ff;//正确
                void (*pf2)(int) = ff;//错误，形参不匹配
                double (*pf3)(int*) = ff//错误，返回值不匹配

18. 以及尾置返回类型的函数：auto f1(int) -> int (*)(int *, int);


**七、抽象数据类型（类）**


1. string isbn() const{return this->bookNo}这里的const表示this是常量指针，不能改变返回值，也不能改变this里面的值
2. friend 友元关键字，最好放在类的头部集中声明，便于阅读，加上friend关键字以后被加上friend的类或者函数便可以访问声明类的私有成员了
3. mutable 关键字：可变数据成员，在类成员前面加上以后，即使类本身是const，那这个成员也可以被修改
4. 前向声明：只声明，不定义，声明的时候只知道有这个类，但是不清楚类里面有哪些成员，通常用于较大工程中文件之间的声明
5. 函数在查找参数的时候，先查找函数作用域的参数，再查找类成员的参数，例如
    int height;
    void Screen::dummy_func(pos ht){
        cursor = width * height;
    }
此时会先找到函数外面的height，而不会先找Screen::height，如果要使用的话，应该：
    void Screen::dummy_func(pos ht){
        cursor = width * this->height;或者 cursor = width * Screen::height;
    }
6. 类的委托构造函数：一个构造函数委托给另一个构造函数：
    class Sales_data{
        public:
            Sales_data(int a, string b):bookNo(a), describe(b){}
            //委托给上面那个构造函数
            Sales_data():Sales_data(1, "123"){}
    }
7. explicit 关键字：用于禁止进行隐式转换，例如在构造函数前面写上以后，函数里面的实参就不允许进行隐式转换了。只对具有一个实参的构造函数有效，不过加了explicit虽然不能进行隐式转换，但是可以进行显示转换，例如使用static_cast
8. 字面值常量类：顾名思义，类里面所有成员都是常量，类必须有一个constexpr的构造函数


**八、IO**


1. unitbuf 操作符：可以在每次输出操作后立即刷新缓冲区,将缓冲区的数据输出出来，例如 cout << unitbuf
2. tie方法：将一个ios对象和另一个绑定起来，例如 cin.tie(&ofs)，就可以每在命令行中输入一个字符，就打印/写入到文件里面。
3. 默认情况下，打开一个ofstream的时候，会把里面原来的文件内容全部丢弃掉，贼坑！！！！解决方法是加一个app模式，例如ofstream app("filename", ofstream::app)//隐含为输出模式 或者ofstream app("filename", ofstream::app|ofstream::out) 这里的app指的是append
4. stringstream对象，其实就相当于一个string的缓冲区。用法：
    ostringstream badNums;
    if(!valid(nums)){
        badNums << nums;
    }
    cerr << badNums.str() << endl;

**九、顺序容器**


1. 因为std标准库里面的容器效率比较低，所以在有替代品的时候，不推荐使用任何一种容器。
2. forward_list 单向链表                只能单向访问，任何位置插入都很快
   vector       可变大小数组            随机访问快，尾部插入元素快，中间插入删除元素慢
   deque        双端队列                随机访问快，头尾插入删除快，中间（可能）慢
   list         双向链表                只能双向顺序访问，任何位置插入都很快
   array        固定大小数组            都很快，但是不能添加删除元素
   string       与vector类似           
3. 迭代器除了.begin() .end()以外还有.cbegin(), .rbegin(), .crbegin(),C指的是const，r指的是反向迭代器
4. array数组类型与普通数组不同的地方在于，array数组类型允许类型赋值
例如：c = {a, b, c}; c2 = c;
5. 顺序容器的assign成员方法：实现顺序容器的拷贝（array除外）：
    list<string> names;
    vector<const char*> oldstyle
    names = oldstyle // 错误，因为类型不同
    names.assign(oldstyle.begin(), oldstyle.end());//正确
    names.assign(10, "Hiya");//替换成10个"Hiya"
6. swap交换的是内部地址，所以假定之前一个指向svec1[3]的迭代器，交换以后将会指向svec2[3]。这种做法会让swap很快，但是array和其他顺序容器不一样，array是真的交换数值的。所以array的swap效率和array里面的元素个数有关。
7. 范围insert：vector<string> v = {"quasi", "simba", "frollo", "scar"}
slist.insert(slist.begin(), v.end() - 2, v.end());//将v的最后两个元素添加到slist的开始位置
svec.insert(svec.end(), 10, "Anna");
slist.insert(slist.end(),{"these", "words"});
8. vector数组在申请内存的时候，会提前申请大于所需的空间，作为备用。因为如果每insert一次就申请内存的话，将会使效率非常低。
9. capacity和size的区别：capacity指的是在不分配新的内存空间时最多可以保存多少元素
10. 容器的适配器（stack，quene 和 priority_quene）
deque<int> deq
stack<int> stk(deq);
stack<string, vector<string>> str_stk(svec);
使用：
    while(!skt.empty()){
        int value = stk.top();
        stk.pop();//还有push以及emplace操作
    }
    quene也是类似的操作。


**十、泛型算法**


1. 泛型算法其实只是我们平时使用容器的类似于find/equal等方法的泛称，由标准库提供
2. 如果不准备改变迭代器的值时，推荐采用cbegin和cend而不是begin和end
3. accumulate的第三个元素决定了使用哪个+运算符，所以accumulate(v.cbegin(), v.cend(), "")是不对的，因为""是const string， 没有重载+号运算符。
4. 一些写入的泛型算法只负责把容器内的值改变，而不申请新的空间，所以经常会出现的错误是：
    vector<int> vec;
    fill(vec.begin(), 10, 0);其中vec实际是空的，这时候fill就会报错
5. back_inserter插入迭代器，可以通过向此迭代器赋值来向容器插入元素
    vector<int> vec;
    auto it = back_inserter(vec);
    *it = 43;//此时就向vec中插入一个元素，vec的大小为1
    或者
    fill_n(back_inserter(vec), 10, 0);//就可以向vec中添加10个元素
6. 自定义排序：谓词
    sort(words.begin(), words.end(), isShorter); 
    bool isShorter(const string &s1, const string &s2){return s1.size() < s2.size()}
7. lambda 表达式：可以理解为未命名的内联函数。
    auto f = []{return 43;};
    f();
    stable_sort(words.begin(), words.end(), [](const string &a, const string &b){return a.size() < b.size()});
    其中捕获列表[]指的是那些明确使用的局部变量，例如
    int sz;
    f = [sz](const string &a){return sz > a.size()};
    其中这个捕获的值是在创建的时候捕获，而不是调用的时候捕获，所以应该尽量减少捕获的值，防止在创建到调用这段时间内变量发生变化。
8. 隐式捕获：f = [=, &os](){}//os是引用捕获方式，其他为值捕获方式
9. lambda表达式的返回类型，需要尾置：
    []() -> int {}
10. lambda的好处在于他本身是可调用对象，可以作为参数存在，如果想要让函数也有这样的功能，可以使用bind方法：
    bool check_size(const int, const string);
    bind(check_size, 1, "str");
    此时就可以作为参数调用了，例如 find_if(word.begin(), words,end(), bind(check_size, 1, "str"))
11. placeholders 关键字和ref关键字，前者用于占位，后者用于生成一个可以拷贝的引用对象。
12. 使用流迭代器istream_iterator<Sales_item> item_iter(cin)可以处理类的输入输出
13. 所有泛型算法的_if版本都是可以接受一个谓词作为判断条件的。


**十一、关联容器（主要是map，set）**

1. map.upper_bound(k)返回一个迭代器，指向第一个关键字大于k的元素，count(t)返回关键字等于k的元素的数量
2. 因为map、multimap等实际上都是有序的，所以在查找某一个元素的时候，可以使用lower_bound和upper_bound来对某一个关键字进行搜索。例如
    for(auto beg = authors.lower_bound(searchItem), end = authors.upper_bound(searchItem);beg!=end; beg++)
3. equal_range函数：一个第二种迭代方式的封装方法：
    for(auto pos = authors.equal_range(searchItem);pos.first!=pos.second; pos.first++)
4. 无序关联容器的本质是使用哈希函数和关键字来进行。存储组织上实际是一组桶，每个桶保存0个或者多个元素。无序容器使用一个哈希函数将元素映射到桶。无序关联容器还有一些管理桶的方法：bucket_count()正在使用桶的个数等。如果想要使用无序关联容器使用自定义类类型作为关键字的话，需要自己使用hash重载关键字，例如：
    size_t hasher(const Sales_data &sd){ return hash<string>()(sd.isbn());}
    bool eqOp(const Sales_data &lhs, const Sales_data &rhs){ return lhs.isbn()==rhs.isbn();}

**十二、动态内存、智能指针**

1. 静态内存：保存局部static变量、类static数据成员、定义在任何函数之外的变量，编译器自动创建和销毁，在使用之前分配，在程序结束后销毁。
   栈内存：保存函数内的非static变量，编译器自动创建和销毁，仅在其定义的程序块运行的时候才存在
   堆内存：又称自由空间，可以用来动态分配内存
2. shared_ptr类：允许多个指针指向同一个对象
除了初始化以外，使用方法和普通指针一样：shared_ptr<string> p1; *p1 = "hi"; swap(p ,q)//交换pq的指针。推荐使用make_shared来进行shared_ptr的创建，相对比较安全：
    shared_ptr<int> p3 = make_shared<int>(42);
3. shared_ptr 有一个问题，就是在最后一个对象销毁前都不会释放内存，所以如果把shared_ptr放在容器中重拍后不需要全部元素的时候，要记得erase那些不需要的元素。




