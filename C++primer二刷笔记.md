再刷一遍primer，记些笔记，查漏补缺

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
19. 函数后面添加const关键字，表示该成员函数不会改变成员中的变量，见下面第一条

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
除了初始化以外，使用方法和普通指针一样：shared_ptr<string> p1; *p1 = "hi"; swap(p ,q)//交换pq的指针。推荐使用make_shared来进行shared_ptr的创建，相对比较安全（能够防止例如将同一块内存绑定到多个shared_ptr上面：
        shared_ptr<int> p3 = make_shared<int>(42);
3. shared_ptr 有一个问题，就是在最后一个对象销毁前都不会释放内存，所以如果把shared_ptr放在容器中重拍后不需要全部元素的时候，要记得erase那些不需要的元素。
4. 使用auto进行变量的初始化（不建议使用）：auto p1 = new auto(obj)//这简直太秀了。。。不管不顾的。。
另外auto p2 = new auto{a, b, c}//错误的，因为auto内只能有单个初始化器。这种做法得到的会是一个指针，例如如果obj是一个int，那么p1就是一个int*
5. 定位new(placemant new) int *p2 = new(nothrow) int//给new传递一个参数，如果内存耗尽，则不抛出异常，返回一个空指针
6. 关于该不该使用智能指针的问题：C++primer中提到：“坚持只使用智能指针，就可以避免所有的内存泄漏问题，所以应该提倡使用智能指针”，但是在网易的分享/培训当中却说道：不要使用共享指针。其主要原因是：共享指针的乱用导致：被shared_ptr的资源实际上并没有共享，这样就会使代码出现资源泄漏和一些bug，而且因为有可能会出现其他程序员通过赋值给另一个共享指针而修改了这一段资源，这样的bug就会很难查出来。另一个原因时shared_ptr并不一定是线程安全的，所以要小心。同时有时候会忘记使用make_share来创建shared_ptr，会导致性能下降以及安全问题。同时经常会出现使用delete把智能指针删除的情况。
另外对于游戏来说不用智能指针更好，因为引用计数本身会带来额外的开销，而且内存分配东一块西一块很不好管理，cache也不友好，最好是对象都放到列表里面，都用数组下标访问，这种方式既容易管理又容易统计还可以把完全不一样的数据结构做出功能上的抽象，比如参考bgfx对于图形API的封装，DX9/DX11/DX12/OpenGL/Vulkan 全都可以用一套API包装起来，texture这样的复杂结构反正也只需要用到一个索引访问----by 果哥
7. 防止野指针：养成在变量离开作用域之前就释放掉或者在delete后将nullptr赋给指针,就表示已经释放掉了但是可能还要用
8. 智能指针不初始化的话,会被初始化成空指针,我们也可以自己初始化:
shared_ptr<int> p2(new int(42));
shared_ptr<int> p1 = new int(1023);//错误，必须使用直接初始化，此时是explicit，我们不能把内置指针隐式转换成智能指针
9. 不要混合使用智能指针和普通指针，会出现很多问题，例如：
        void process(shared_ptr<int> ptr){}
        int *x(new int(1024))
        process(x)//错误：x是普通指针
        process(shared_ptr<int>(x))//合法的，但是内存会被释放
        int j = *x //未定义的，x是空指针
10. 不要使用智能指针的get方法返回的内置指针初始化另一个指针，或者给智能指针复制。所以需要在确定程序不会delete get到的指针时才用get
11. 如果在程序崩溃发生异常的时候，此时new出来的对象没有delete，则内存永远不会释放，，，，
12. 使用自己的删除器：shared_ptr<connection> p(&c, end_connection);这样在p被销毁的时候，会调用end_connection，从而通过我们自己的定制操作关闭网络连接等。
13. unique_ptr是不能拷贝和复制的，但是可以通过release和reset将指针的所有权从一个非const的unique_ptr转移给另一个unique。release会切断unique_ptr和原来对象间的关系，reset是让unique_ptr重新指向给定的指针。
p2.release()//错误，p2不会释放内存而且我们也丢失了指针
auto p = p2.release()//正确，但是我们要记得delete(p)
p3.reset(p2.release())//p2内存交给p3
14. weak_ptr：一种shared_ptr，但是不会增加计数，当shared_ptr释放掉，weak_ptr也释放掉。因为这种特性，在使用weak_ptr的时候要先用lock判断对象是否还存在：if(shared_ptr<np> np = wp.lock()){}
15. 对动态数组的初始化：int *p = new int[10]()//10个都是0的int
如果使用 int *p = new int[0]//仍然不会报错，但是p是一个类似于尾后指针的非空指针。
16. 智能指针可以支持动态数据，但是只有unique_ptr支持直接下标访问，shared_ptr想要访问的话必须提供自己的删除器，并且通过get来修改数组。
17. new和delete将对象构造/析构和内存申请/释放结合在了一起（某道面试题），因此allocator可以将两个分开来。：
        allocator<string> alloc;//可以分配string的allocator对象
        auto const p = alloc.allocate(n);//可以分配n个未初始化的string
        alloc.construct(p++);//p为空字符串
        alloc.destroy(--p);//销毁，这里其实应该有一个while的
        auto q = uninitialized_copy(vi.begin(), vi.end(), p);//拷贝数据
        alloc.deallocate(p,n)//释放内存

**十三、（对象）拷贝控制**

1. 对于拷贝构造函数是explicit的构造函数来说，使用拷贝初始化还是直接初始化是有很大不同的：
        vector<int> v1(10); //正确，直接初始化
        vector<int> v2 = 10;//错误，接受大小参数的构造函数是explicit的
        void f(vector<int>);//f的参数进行拷贝初始化
        f(10);              //错误。不能用一个explicit的构造函数拷贝一个实参
        f(vector<int>(10)); //正确：从一个int直接构造一个临时vector
因为vector的接受单一大小参数的构造函数是explicit的，所以会出现错误，必须显式进行调用
2. 析构函数：先执行函数体，再按照成员初始化的逆序销毁。如果需要一个析构函数，一般情况下也需要一个自定义拷贝赋值运算符和拷贝构造函数，这三个几乎是同时存在的，其中一个必需时，另外两个一般也必需
3. 阻止拷贝：虽然声明了他们，但是不能用任何方式使用他们：
        noCopy() = default;//使用合成的默认构造函数
        noCopy(const noCopy&) = delete //阻止拷贝
        noCopy &operator=(const noCopy&) = delete; // 阻止赋值
        ~noCopy() = default;// 使用合成的析构函数
        析构函数不能是delete的，因为如果析构是delete的，那对象无法销毁了
4. 对于成员变量有const的，或者有引用成员的，编译器无法对类进行默认构造函数。
5. 关于std::swap 和 using namespace std; swap()的区别：
如果使用前者的话，则每次调用一定会使用std版本的swap，而如果使用后面这种写法，就可以在类内定义swap，然后调用的时候会先检查类内部是否有swap，如果没有的话才调用std版本的swap，这个小技巧还是要知道的，使用std::move可以避免潜在的名字冲突
6. move标准库函数：调用移动构造函数，相当于一个static_cast，但是同时可以将原来的str移走，需要使用str::string t(str::move(r));如果单纯使用string&&r = std::move(r)的话，并不能移走
move可以获得绑定到左值上的右值引用
7. 右值引用：必须绑定到右值的引用， 即只能绑定到一个即将销毁的对象
        int i = 42;//正确
        int &r = i;//正确， r引用i
        int &&rr = i;//错误，不能将一个右值引用绑定到左值上面，这里面i是一个左值
        int &r2 = i*42;//错误 i*42是一个右值
        const int &r3 = i * 42;// 正确，我们可以将一个const的引用绑定到一个右值上面
        int &&rr2 = i*42;// 正确，将rr2绑定到乘法结果上。因为i*42是一个右值
        本质其实是因为i*42 是一个右值，i是左值
        int &&rr1 = 42;//正确，字面常量是右值
        int &&rr2 = rr1//错误，表达式rr1是左值
8. noexcept关键字：告诉我们的函数将不会抛出任何异常：
        StrVec::StrVec(StrVec &&s) noexcept:element(s.element){}
        不抛出异常的移动构造函数和移动赋值运算符必须标记为noexcept
9. 定义了一个移动构造函数或者移动赋值运算符的类必须也定义自己的拷贝操作，不然有些成员会被默认的定义成delete的。而如果定义了拷贝构造函数没定义移动构造函数的时候，使用了move方法， 会仍然执行拷贝构造
        Foo z(std::move(x))执行拷贝构造
10. 对于同时存在拷贝构造函数和移动构造函数的时候，编译器对两者的选择是看是否是右值或者是左值的，右值是移动，左值是拷贝，例如
        StrVec v1, v2;
        v1 = v2 //v2是一个左值，使用拷贝赋值
        StrVec getVec(istream &)//返回一个右值，此时拷贝和移动都是可行的，但是因为调用拷贝赋值需要进行一次到const的转换，而StrVec&是精确匹配的，所以会用移动赋值
        v2 = getVec(cin)//是一个右值，选择移动赋值
11. 调用make_move_iterator可以讲一个普通的迭代器转换成一个移动迭代器，这个迭代器可以返回右值：
        auto first = alloc.allocate(newcapacity);
        auto last = uninitialized_copy(make_move_iterator(begin()), make_move_iterator(end()), first);
12. 引用限定符&（reference qualifier）放在函数的参数列表后面，表示this可以指向一个右值还是左值
        Foo &operator=(const Foo&)& //只能向可修改的左值赋值
        Foo &operator=(const Foo&)&&//只能向右值赋值
        如果和const一起使用的话，const必须在前：
        Foo someMem()const &;
        同时如果重载的话，必须所有的版本都必须加上引用限定符或者不加
        Foo sorted() &&
        Foo sorted() const//错误，因为上面有引用限定符，所以这里必须也有


**十四、重载和类型转换**

1. 是否将重载运算符定义为成员函数（或者普通函数）
        赋值（=）， 下标([]), 调用（()）和成员访问箭头必须是成员函数
        复合赋值运算符一般来说应该是成员，但并非必须
        改变对象状态的运算符或者与给定类型密切相关的运算符，如递增，递减，解引用运算符，通常是成员
        具有对称性的运算符可能转换任意一端的运算对象，如相等性，加减乘除，关系运算符，普通的非成员函数
2. 一个类如果有下标运算符重载的时候operator[](),一般会有两个版本，一个是const版本，另一个是非const版本，这样当我们给对象赋值的时候使用非常量的，当我们只是作为常量返回的时候就不能赋值
3. 如何区分递增++，递减--的前置还是后置：后置版本提供一个值为0的实参（虽然实际上这个实参没什么用，只是用来区分前置和后置的）
        StrBlobStr operator++(int)//后置运算符 a++
        StrBlobStr& operator++()//前置运算符 ++a
4. 重载解引用运算符(*)和箭头运算符（->）的时候,解引用运算符可以返回任意我们想要的数据，例如operator*(){return 42;},但是箭头运算符则必须指向类对象的指针或者是一个重载了->的累的对象，除此以外都会发生错误
5. 函数调用运算符的重载：即可以通过对类的调用来完成一些操作：
        struct absInt{
            int operator()(int val)const{
                return val < 0? -val:val;
            }
        }
        int i = -42;
        absInt absObj;
        int ui = absObj(i);
        其作用类似于lambda表达式
6. 标准库function类型：实际上是一个模板：
        function<int(int, int)> f1 = add;//int(int, int)是一个函数类型，接受两个int，返回一个int,add是一个函数指针
        function<int(int, int)> f2 = divide();//divide是类似上一条的对象类的对象
        function<int(int, int)> f3 = [](int i, int j){return i*j};//lambda
        cout<< f1(4, 2)<<endl;
        map<string, function<int(int, int)>> binops;然后就可以添加任意形式的可调用对象了
        例如binops["+"] = std::add<int>();
7. 重载函数无法放到map里面，可以的做法是将函数指针放进去，例如：
        int (*fp)(int, int) = add;
        binops.insert({"+", fp});
8. 一般不会编写隐式的类型转换运算符，而可以编写显式的类型转换运算符，并且应该尽量避免有二义性或者容易引起误解的类型转换。
        例如同时编写一个 operator int()const 和 operator double() const的时候，调用long double时就会出现二义性

**十五、面向对象程序设计**

1. 养成写override 关键字的习惯，指明该函数是从基类中改写的。
2. 动态绑定：根据调用的实参类型选择到底是使用父类的成员函数还是子类的成员函数：
        class Buik_quote:public Quote{    }
        double print_total(const Ouote &item){}//动态绑定
        print_total(basic)//调用父类
        print_total(bulk)//调用子类
3. 派生类和基类的存储空间是不连续（可能需要考虑到cache存储）
4. 如果一些类不想让其他类继承，可以使用final关键字：
        class NoDerived final {}//这个类不能被继承
5. 从派生类到基类的转换：自动类型转换只对指针和引用类型有效，同时忽略派生类独有的对象。
   基类向派生类不存在隐式类型转换
和任何其他成员一样，派生类向基类的类型转换也可能因为由于访问受限而变得不可行。
6. 多态性：具有继承关系的多个类型成为多态类型。当我们使用基类的引用或者指针调用基类中定义的一个函数时，我们并不知道该函数真正作用的对象是什么类型。
7. 使用作用域运算符可以实现强迫虚函数执行某个特定版本的功能：double undiscounted = baseP->Quote::net_price()
8. 纯虚函数：即表示当前这个函数时无意义的，拥有纯虚函数的类被称为“抽象基类”，是无法定义对象的，只能用来继承
        class Disc_quote : public Quote{
            double net_price() const = 0;
        }
        Disc_quote discounted;//错误
9. 派生访问说明符：控制派生类用户对于基类成员的访问权限：
        class Base{
            public:
                void pub_mem();
            protected:
                int prot_mem;
            private:
                char priv_mem;
        }
        class Pub_derv:public Base{
            int f(){return prot_mem}//正确，派生类可以访问protect
            char g(){return priv_mem;}//错误，private不可访问
        }
        class Priv_derv:private Base{
            int f(){return prot_mem}//正确，派生类可以访问protect
            char g(){return priv_mem;}//错误，private不可访问
        }
        但是在对象对于基类成员的访问时就会出现不同，即所有父类的成员都是private或者public的：
        Pub_derv pub_d1;
        Pirv_derv priv_d2;
        pub_d1.pub_mem();//正确，因为是public
        priv_d2.pub_mem();//错误，因为是private的
        类型转换也同样适用
10. 友元friend关系是不能继承的，不能因为父类是friend，所有的派生类都是friend。
11. 改变个别成员的可访问性：有时候我们需要改变个别成员的可访问性，可以使用using关键字来解决：
        class Derived : private Base{
            public:
                using Base::size;
            protect:
                using Base::n;
        }
        通过这样的写法，使用Derived的用户可以直接访问Base的size成员，即使是private继承的。同时Derived的派生类可以访问n这个成员。
12. 同成员访问说明符一样，struct和class在继承的时候也是按照默认的权限继承的，例如所有的class默认都是private继承，所有的struct默认都是public继承。
13. 因为名字查找永远优先于类型检查，所以当名字相同的时候会出现下面的问题：
        struct Base{
            int memfcn();
        }
        struct Derived:Base{
            int memfct(int);
        }
        Derived d;
        d.memfcn()//错误，因为参数列表为空的memfcn被隐藏掉了。正确的用法是d.Base::memfcn()
14. 虚析构函数：
        class Quote{
            virtual ~Quote() = default;
        }
        如果一个类定义了虚析构函数，那么即使它通过=dafault的方式使用了默认的版本，编译器也不会为这个类定义默认的移动操作。
15. 基类和派生类的构造-析构顺序，假设A继承自B，B继承自C：
        构造顺序：A调用B的构造函数，B调用C的构造函数
                 C执行构造函数，B执行构造函数，最后A执行构造函数
        析构顺序：A销毁自己的成员，调用B的析构函数，B销毁自己的成员，调用C的析构函数
16. 派生类可以使用从父类继承来的构造函数：
        class Bulk_quote : public Disc_quote{
            public:
            using Disc_quote:: Disc_quote//继承Disc_quote的构造函数
        }
        其实类似于构造函数里面成员变量初始化的 Bulk_quote():Disc_quote(){}
17. 如果想要在vector既存放父类又存放派生类，如果使用vector<Base>，那么派生类的部分将会被扔掉，如果使用vector<child>那么父类无法存放。正确的做法应该是存放父类的指针：vector<shared_ptr<Base>>


**十六、模板与泛型编程**

1. template<typename T, typename U, class M>//必须使用class 或者typename，建议使用typename
   int compare(const T &v1, const T &v2){} 
2. 非类型模板参数：
        template<unsigned N, unsigned M>
        int compare(const char (&p)[N], const char (&p2)[M])
        当我们调用compare("hi", "mom")的时候
        会生成int compare(const char (&p)[3], const char (&p2)[4])，考虑到末尾有一个空字符
3. 也可以是inline的,但是要在模板参数列表之后，返回类型之前：
        template<typename T> inline T min(){}
4. 编写泛型代码的要求：模板中的函数参数是const的引用；函数体中的条件判断仅使用“<”或者"less"比较运算，这样可以少一种类型的支持，让代码运行更快，尽量减少对实参类型的要求
5. 当编译器遇到一个模板定义的时候，他并不生成代码，只有出现一个实例的时候才生成代码。而为了生成一个实例化版本，编译器需要掌握函数模板或者类模板成员函数的定义，所以
        模板的头文件通常既包括声明也包括定义
6. 模板类：
        template <typename T> class Blob{
            Blob();
            Blob(std::initializer_list<T> il);
        }
        Blob<int> ia = {0, 1, 2, 3, 4};
7. 在模板类的内部的作用域内，我们可以直接使用模板名而不必指定模板实参
8. 在模板类中声明友元：
        template <typename> class BlobPtr;
        template <typename> class Blob;
        template <typename T> bool operator==(const Blob<T>&, const Blob<T>&);
        template <typename T> class Blob{
            friend class BlobPtr<T>;
            friend bool operator==<T>(const Blob<T>&, const Blob<T>&);
        }
        这样友元的声明用Blob的模板形参作为他们自己的模板实参，friend的关系就被限定在相同类型实例化的Blob和BlobPtr相等运算符之间
        如果使用不同的模板参数，例如：friend bool operator==<typename X>();则所有的实例都将成为友元
        或者将模板类型参数声明为友元，例如friend T;
9. 模板的类型别名，除了传统的类型别名以外，还可以使用：
        template<typename T> using partNo = pair<T, unsigned>;
        partNo<string> books; books是一个pair<string, unsigned>
10. 当我们希望通知编译器一个名字表示类型时，必须使用关键字typename而不是class：
        return typename T::value_type();
11. 普通类和模板类的模板成员：
        class DebugDelete{
            template<typename T> void operator()(T *p)const{delete p;}
        }
        double *p = new double; DebugDelete d;
        d(p);//通过类来进行delete
        template <typename T> class Blob{
            template<typename It>Blob(It b, It e);
        }
12. 显式实例化：
        extern template class Blob<string> //实例化声明
        template int compare(const int&, const int&) //实例化定义
        这样可以避免在多个文件中实例化相同的模板，避免不必要的开销。
        对于每一个实例化的声明，在程序中某个位置必须有其显式的实例化定义
13. 顶层（top）const在模板中的实参转换通常会被忽略：
        template <typename T> T fobj(T, T);
        template <typename T> T fref(const T&, const T&);
        int a[10], b[42];
        fobj(a, b);//调用f(int*, int*);
        fref(a, b);//错误，数据类型不匹配
14. 显示模板参数：例如template<typename T1, typename T2, typename T3>
                        T1 sum(T2, T3);
        使用的时候：auto val = sum<long long>(i, j);//这里的long long指的是T1
        三个参数必须从左向右匹配：如果是这样写的：
                        template<typename T1, typename T2, typename T3>
                        T3 sum2(T2, T1);
        则必须制定所有三个模板，因为无法只使用一个的时候无法确定到底是T1还是T3，所以一定要按照顺序来
                        用的时候只能：auto val = sum2<long long, int, long>(i, j);
                    其中 long long指的是T1，int指的是T2， long是T3
15. 组合使用类型转换模板remove_reference、尾置返回、decltype，我们可以在函数中返回元素值的拷贝：
        template <typename It>
        auto fcn2(It beg, It end) -> typename remove_reference<decltype(*beg)>::type{return *beg;}
16. 函数指针实参推断：
        template <typename T> int compare(const T&, const T&);
        int (*pf1)(const int&, const int&) = compare;//pf1指向实例int compare(const int&, const int&);
        void func(int(*)(const int&, const int&));
        void func(int(*)(const string&, const string&));
        func(compare<int>);;//显式的指出实例化哪一个compare
17. 引用折叠：在正常绑定规则之外有两个例外规则：
        (1). template <typename T>void f3(T&&);
        当我们将一个左值（如i）传递给函数的右值引用参数，且此右值引用指向模板类型参数（如T&&)时，编译器推断模板类型参数为实参的左值引用类型，因此当我们调用f3(i)的时候，编译器推断T的类型为int&而不是int
        (2). 如果简介创建一个引用的引用，（不是直接使用&&，而是通过类型（1）的这种转换创建的，类型别名或者模板参数），则这些引用形成了折叠，在所有情况下，引用会折叠成一个普通的左值引用类型。
        X& &， X& &&和X&& X都折叠成X&，X&& &&折叠成X&&
        这就会引起非常多的问题，例如调用f3(42);因为是右值，所以实际上里面T为int，而调用f3(i);时，T是int&，就可能改变i的值。所以正确的做法是重载一下：
        template <typename T>void f(T&&);
        template <typename T>void f(const T&);

18. 通过引用折叠实现标准库move函数;
        template <typename T>
        typename remove_reference<T>::type&& move(T&& t){
            return static_cast<typename remove_reference<T>::type&&>(t);
        }
        请自己分析一下调用 std::move(string("bye"))和std::move(s);在move里面的步骤//具体答案在p611
        这种方法可以同时适配左值和右值，根据具体情况来具体指示到底是左值版本还是右值版本。
19. forward关键字：可以保持原始实参的类型，保存实参类型的所有细节：
        template <typename F, typename T1, typename T2>
        void flip(F f, T1 &&t1, T2 &&t2){
            f(std::forward<T2>(t2), std::forward<T1>(t1));
        }
        此时如果我们调用flip(g, i, 42);i将以int&类型传递给g，42将以int&&类型传递给g
20. 当重载和模板同时发生的时候，编译器会选择非模板的版本，因此当两个模板都可以精确匹配某一次调用的时候，为了保证精确调用某一个模板，建议声明一个非模板版本（当然我觉得这里尽量不要出现这种情况，，，这样代码的可读性也太emmmm）
21. 可变参数模板：
        template <typename T, typename... Args>
        void foo(const T &t, const Args&, ...rest);
        当使用foo(i, s, 42, d);的时候，编译器会生成void foo(const int&, const string&, const int&, const double&);的版本
        也可以使用sizeof...(Args)（表示类型参数的数目） 和sizeof...(rest)（表示函数参数的数目） 
22. 可变参数函数在调用的时候通常是递归的，例如上面那个，会首先调用foo(i,s,42,d),然后调用(i,42,d),以此类推
23. emplace_back实际上是一个可变参数模板，然后内部进行了转发参数的操作：
        template <class...Args> inline
        void StrVec::emplace_back(Args&&... args){
            chk_n_alloc();
            alloc.construct(first_free++, std::forward<Args>(args)...);
        }
        实际上调用emplace_back(10, 'c')的时候会扩展出std::forward<int>(10), std::forward<char>(c)
24. 函数和类模板的特例化：template<typename T>int compare(const T&, const T&);
                            template <> int compare(const char* const &p1, const char* const &p2);

**十七、标准库特殊设施**

1. tuple类型：类似于pair的模板，但是一个tuple可以有任意数量的成员。其实tuple的左右有些类似于class和struct
        tuple<string, vector<double>, int, list<int>> someVal("constants", {3.14, 2.71}, 42, {0, 1, 2, 3});
        tuple的初始化要么使用直接初始化方法，要么使用make_tuple方法生成
        获取tuple的成员：auto book = get<0>(someVal);
        如果不知道tuple准确的类型，可以使用decltype，tuple_element和tuple_size：
        size_t sz = tuple_size<decltype(someVal)>::value;//返回4
        tuple_element<1, decltype(someVal)>::type cnt = get<1>(someVal);//cnt是一个vector
2. bitset类型：是一个能够处理最长整型类型大小的位集合：
        bitset<32> bitvec(1U);//低位为1，其他为0，编号从0开始的二进制位是低位。31为高位
        用string初始化bitset的话，正好和string的下标相反：
        bitset<32> bitvec4("1100");2,3位为1，剩余两位为0；
3. 正则表达式（regex类）：
        regex r("[[:alpha:]]*" + "[^c]ei" + "[[:alpha:]]*");
        smatch results;
        if(regex_search(test_str, results, r)){cout<<results.str()<<endl;}
        因为正则表达式是在运行的时候编译的，所以效率非常的慢，应该尽可能少的使用正则表达式
4. 正则表达式迭代器：
        for(sregex_iterator it(file.begin(), file.end(), r), end_it; it != end_it; ++it){
            cout<<it->str<<endl;//输出所有匹配的单词
            it->prefix().length()//前缀大小
            it->suffix().str().substr(0, 40);//后缀的一部分
        }
5. 和rand不一样的随机数引擎类：
        default_random_engine e;
        e.seed(time(0));//可以不设置
        for (size_t i = 0; i < 10; i++){
            cout << e() <<endl;
        }
6. 随机数分布类：
            uniform_int_distribution<unsigned> u(0, 9);
            default_random_engine e;
            for(size_t i = 0; i < 10; ++i){
                cout << u(e) <<
            }
        这些类应该全部定义成static的，这样才能保证每次调用返回的结果不一样。
7. IO库：改变输入输入格式的状态
            cout<< boolalpha << true << " " << false <<endl;
        会输出 true false 而不是 1 0，这时候要用cout<< noboolalpha才行。类似的还有hex，oct，dec等
        以及在cout上面的cout.precision(12); 
8. 底层的未格式化IO操作（注意，这些底层的操作容易出错）：
        cout.put(ch)//单字节操作，可以保留空白字符的
        peek返回下一个字符的副本，但是不会从输入流中把这个字符删除掉
        unget使输入流向后移动，从而最后读取的值又回到流中//不过这些操作的返回都是int类型的，方便判断是否是文件结尾
        cin.getline等操作是多字节IO操作，可以比较快速的输入


**十八、用于大型程序的工具**  

1. 栈展开（stack unwinding）：在抛出一个异常的时候，程序会暂停当前函数的执行并且找对应的catch，如果找不到，就继续检查外层的catch。在展开的过程当中，因为调用链上的语句会提前退出，所以调用链上的局部对象可能会销毁掉。同时如果一些需要手动释放的资源在释放之前发生了异常，那么这些资源将不会释放，我们写代码的时候需要注意
2. 异常的重新抛出：当一块catch语句语法解决某个异常的时候，可以将这个异常抛出给上一层函数处理：

        catch(my_error &eObj){
            eObj.status = errCOdes::servereErr;
            throw;抛给上一层
        }

3. 捕获所有异常：

        catch(...){    }

4. 处理构造函数初始值抛出的异常

        template <typename T>
        Blob<T>::Blob(std::initializer_list<T> il)
        try:
        data(std::make_shared<std::vector<T>> (il)){
        }
        catch(const std::bad_alloc &e){
            handle_out_of_memory(e);
        }

5. noexcept关键字：
    告诉程序这个地方不会抛出异常（即使抛出异常也会终止整个程序）

        void recoup(int) noexcept;
        void recoup(int) throw();和上面是等价的。

    判断一个函数是不是会抛出异常的：

        noexcept(recoup(i));//返回true
        void f() noexcept(noexcept(g()));//让f和g的异常说明一致

    noexcept说明符会影响函数指针的使用，例如加入noexcept的函数指针和没有加入noexcept的函数指针不能相等
6. 定义命名空间：

        namespace cplusplus_primer{
            class tempClass{
            };
        }//是可以没有分号的

    注意不要随便写namespace xxx,会导致名字混乱
7. 模板特例化：模板特例化必须定义在原始模板所属的命名空间中

        namespace std{
            template<> struct hash<Sales_data>;
        }

    模板特例化以后，然后就可以在命名空间外部定义它了

        template<> struct std::hast<Sales_data>{}

8. 嵌套的命名空间：

        namespace cplusplus{
            namespace QueryLib{

            }
            QueryLib::XXX//前面需加内部命名空间名
        }
        cplusplus::QueryLib::XXX

9. 内联命名空间：

        namespace cplusplus{
            inline namespace FifthEd{//必须出现在命名空间第一次定义的地方
                XXX;
            }
        }
        cplusplus::XXX;//可以不写内部内联的命名空间名

10. 未命名的命名空间：
未命名的空间只在一个文件中起作用，不同文件中的为命名空间互相不冲突。所以一个文件中只能有一个未命名空间，且他本身类似于静态的作用

        int i;
        namespace{
            int i;
        }
        i = 10;//错误，因为i的定义即出现在全局作用域中，又出现在未嵌套的未命名的命名空间中
        namespace local{
            namespace{
                int i;
            }
        }
        local::i = 42//正确，定义在嵌套的未命名的命名空间中的i与全局作用域的i不同

11. 代替using namespace，使用类型空间的别名：

        namespace primer = cplusplus_primer;
        primer::XXX;

12. 友元声明和实参相关的查找：

        namespace A{
            class C{
                friend void f2();//没有形参，很可能找不到
                friend void f(const C&);//根据实参相关的查找规则可以被找到
            }
        }
        A::C obj;
        f(obj);//通过在A::C中的友元声明和obj实参找到f
        f2();//找不到，没有形参

13. 多继承,多继承的类包含所有基类的成员，但是基类的构造函数形参不能相同：

        class Bear:public ZooAnimal{
            Bear(const string&);
        }
        class Endangered{
            Endangered(const string&);
        }
        class Panda:public Bear, public Endangered{
            因为两个基类构造函数形参相同，所以必须重新定义构造函数
        }

14. 多继承的二义性，当两个基类有相同的成员名字，将会引发二义性，建议定义一个全新的版本
15. 虚继承：对某个类作出声明，承诺愿意共享他的基类，其中共享的基类称为虚基类

        class Raccoon : public virtual ZooAnimal{}
        class Bear    : virtual public ZooAnimal{}
        class Panda   : Public Bear, public Raccoon, public Endangered{}

在普通情况下，如果不是虚继承的话，Panda将会有两份ZooAnimal，现在虚继承下，就只会有一份ZooAnimal了
这个时候当我们创建一个Panda对象的时候，首先构造虚基类ZooAnimal，接下来构造Bear，然后构造Raccoon，然后构造第三个直接击雷Endangered，最后构造Panda。因为虚基类总是先于非虚基类构造，和他们继承的顺序无关。多个虚基类同时存在的时候，则按照顺序来

**十九、特殊工具和技术**  

1. new和delete的实现步骤：

        string *sp = new string("a value");
        1. new表达式调用一个名为operator new（或者operator new[]）的标准库函数
        2. 标准库函数分配一块足够大的、原始的、未命名的内存空间以便存储特定类型的对象（或者对象的数组）
        3. 编译器运行相应的构造函数以构造这些对象，并为其传入初始值
        4. 对象被分配了空间并且构造完成，返回一个指向该对象的指针。
        delete sp;
        1. 对sp所致的对象或者数组执行对应的析构函数
        2. 编译器调用名为operator delete（或者delete[]） 的标准库释放内存空间 

2. 自定义自己的new和delete：必须使用noexcept保证他不会出错，返回类型必须是void*，第一个形参必须是size_t类型且不能包括默认实参

        void *operator new(size_tw size){
            if(void*mem==malloc(size)){
                return mem;
            }
            else{   }
        }

3. 显示调用析构函数：

            string *sp = new string("a value");
            sp->~string();
            跟destroy(类似)
 
4. 运行时类型识别（RTTI功能）由两个运算符实现：

        typeid运算符，用于返回表达式的类型
        dynamic_cast运算符,用于将基类的指针或者引用安全的转换成派生类的指针或引用
        假如Base类至少含有一个虚函数，Derived是Base的public派生类，如果有一个指向Base的指针bp，则我们可以在运行时将它转换成指向Derived的指针：
        if(Derived *dp = dynamic_cast<Derived*> (bp)){}
        if(typeid(*bp) == typeid(*dp)){}//表达具体是什么类型，typeid作用于对象，所以我们用*bp
5. 给枚举指定类型：
        
        enum intValues:unsigned long long{
            charTyp = 255;.......
        }
        以及可以像类一样进行提前声明
        enum class IntValues{}//限定作用于的枚举类型，在括号外如果没有对象的话就不可访问

6. 枚举类型的形参匹配：
        
        enum Tokens{INLINE = 128, VIRTUAL = 129};
        void ff(Tokens);
        void ff(int);
        int main(){
            Tokens curTok = INLINE;
            ff(128);   //匹配int
            ff(INLINE);//匹配ff(Tokens)实参匹配！！
            ff(curTok);//匹配ff(Tokens)
        }
7. 类成员指针：使用->\*和.\*来进行访问
8. 与function关键字类似的：mem_fn标准库功能，可以自动推断可调用对象的类型;
        
        find_if(svec.begin(), svec.end(), mem_fn(&string::empty));

9. union:一种节省空间的类，其中可以有多个数据成员，但是在任意时刻只能有一个数据成员有值，给他某个成员赋值以后，其他的成员会变成未定义的，union不能含有引用类型的成员。因为union不能作为基类或者继承自其他类，所以他也没有虚函数。

而匿名union则是一个未命名的union，并且在右花括号和分号之间没有任何声明，一旦我们定义了一个匿名union，编译器就自动的为该union创建一个未命名的对象,在匿名union定义所在的作用域内，该union里面的成员都是可以直接访问的。

        union{
            char cval;
            int ival;
            double dval;
        };
        cval = 'c';
        ival = 42;

10. 使用类来管理union：
当union包含内置类型的成员时，我们可以使用普通的赋值语句改变union保存的值，但是当含有特殊类类型成员的union就没那么简单了，我们如果需要将union的值改为类类型成员对应的值，就需要构造或者析构该类类型的成员

同时如果是包含内置类型的成员，编译器将按照成员的次序依次生成默认构造函数或者拷贝控制成员。

因为有些复杂，所以通常将union放在类里面而不是类放在union里面

        class Token{
        public:
                //因为union含有一个string这样的类成员，所以Token必须定义拷贝控制成员
                Token() : tok(INT), ival{0}{

                }
                Token(const Token &t) : tok(t.tok){
                        copyUnion(t);
                }
                Token &operator=(const Token&);
                //如果类内有一个string这样的类成员，我们必须销毁他
                ~Token(){
                        if(tok==STR)
                            sval.~string();
                }
                //下面的赋值运算符负责设置union的不同成员
                Token &operator=(const std::string&);
                Token &operator=(char);
                Token &operator=(int);
                Token &operator=(double);
        private:
                enum{INT, CHAR, DBL, STR} tok;//判别式，用来辨认union存储的值
                union{
                        char cval;
                        int ival;
                        double dval;
                        std::string sval;
                };//每个Token对象含有一个该未命名Union类型的未命名成员
                void copyUnion(const Token&);
        };

        类的赋值运算符将负责设置tok并且为union的相应成员赋值，和析构函数一样，这些运算符在为union赋新值前必须首先销毁string：

        Token &Token::operator=(int i){
                //如果union的当前值是string，那么我们必须先调用string的析构函数销毁这个string，然后才能赋新值
                if(tok == STR){
                        sval.~string();
                }
                ival = i;
                tok = INT;
                return *this;
        }

11. 局部类无法访问其外层作用域的局部变量等
12. 位域：类可以将非静态的数据成员定义成位域(bit field)一个位域中含有一定数量的二进制位，位域的类型必须是整形或者枚举类型

        typedef unsigned int Bit;
        class File{
                Bit mode : 2;
                Bit modified : 1;
                Bit prot_owner : 3;
                Bit prot_group : 3;
                Bit prot_world : 3;
        public:
                enum modes{READ = 01, WRITE = 02, EXECUTE = 03};
                ......
        }
13. volatile 限定符：和const是一个性质的，不过具体含义和机器系统有关
14. 链接提示：extern"C":需要用到调用其他语言编写的函数的时候：

        例如可能出现在C++头文件<cstring>中的链接提示;
        extern "C" size_t strlen(const char*);
        extern "C"{
                int strcmp(const char*, const char*);
        }
15. 导出C++到其他语言：

        extern "C" double calc(double dparm){....}//将会为该函数生成适合于C的代码



