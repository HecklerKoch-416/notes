# c++学习笔记
# 引用
引用：引用的本质是取别名。
 
`int &a = b;//  a是b的别名，a与b表示同一块内存空间`
 
引用必须初始化，引用初始化后不可更改。
 
引用作为函数参数：与地址作为参数起一样的作用。
 
引用作为函数返回值：不能返回局部变量的引用；函数返回引用时可以作为左值。
 
 ```
 int &test(){
     static int a = 1;
     return a;
 }
  
 int main(){
     int ref=test();
     cout << ref << endl;//输出结果为1
     test()=2;//test()是返回值a的引用，修改test()即修改a
     cout << ref << endl;//输出结果为2
     ...
 }
 ```
  
  常量引用的作用：修饰形参，防止误操作。
  
  ```
 void test(const int &a){
     //a++; 报错
 }    
 ```
 
 # 函数高级
 函数高级：
 
 函数默认参数：如果从某个函数开始有默认值，那么后面的所有参数都要有默认值；函数声明时有默认值，则定义时不能在加上默认值。
 ```
 int test(int a,int b=10,int c=20){
     return a+b+c;
 }
 
 int main(){
     test(1);//可以调用,返回31
     test(1,2,3);//返回5
     ...
 }
 ```
 
 占位参数：为以后程序的扩展留下线索，兼容C语言程序中可能出现的不规范写法
 
 ```
 int test(int a,int);
 ```
 
 函数重载：
 
 条件：在同一作用域下，函数名称相同，参数类型、顺序、个数不同。（返回值不同不作为函数重载条件）
 
 引用作为函数重载：可以用常量引用做区分
 
 ```
 void test(int &a) {cout << 1;}
 void test(const int &a) {cout << 2;}//可以发生重载
 
 int main(){
     int a=10;
     test(a);//打印1
     test(10);//打印2
     ...
 }
 ```
 
 重载与默认参数：
 ```
 void test(int a,int b=10){
     cout << 1 << endl;
 }
 void test(inta){
     cout << 2 << endl;
 }
 int main(){
     int a=10;
     test(a);//产生了二义性，程序员应该避免这种情况
     ...
 }
 ```
 
 # 类和对象
 类和对象
 
 C++面向对象三大特性：封装、继承、多态
 
 ## 封装
 封装：将属性和行为作为整体；权限控制。
 
 class与struct区别：默认访问权限不同，class默认是私有权限,struct默认是公共权限。
 
 成员属性私有化的作用：1.控制读写权限；2.在写时可以检测数据有效性。//只能由成员函数修改私有属性
 
 ### 构造和析构
 
 构造函数与析构函数：构造函数负责初始化，析构函数负责清理。如果没有手动定义，编译器会自动生成构造函数和析构函数。
 构造函数和析构函数由编译器调用，不能由用户调用。构造函数和析构函数无类型，无返回值，可以有不同参数(重载)。
 
 ```
 class Student{
 public://公共访问权限，均可访问。
     string s_name;
     unsigned int s_age;
     int s_uid;
     int *s_height;
     void showMessage(){
         cout << "姓名:" << s_name << "  年龄:" << s_age << "  学号：" << s_uid << endl;
     }
     void changeScore(int score){//如何访问保护权限的s_score? 利用公有的函数修改。这里提供了s_score的读接口
         this->s_score = score;    //this指针指向该类实例化的对象
     }
     void showScore(){
         cout << "成绩：" << s_score << endl;
     }
     void changeId(int id){
         this->s_id=id;
     }
     void showId(){
         cout << "身份证号:" << s_id << endl;
     }
     Student(){//构造函数，程序在调用对象时自动调用构造函数，有且只有一次。函数名为类名。
         cout << "无参构造" << endl;
     }
     Student(int name,int age,int uid,int score,int id){
         cout << "有参构造" << endl;
     }
     Student(const Student &s){//拷贝构造。const是防止作为参数对象被修改。为什么要引用？ 值传参会导致实例化，形成递归而无限循环。
         cout << "有参构造" << endl;
     }
     
     ~Student(){//析构函数，程序在对象销毁前自动调用析构函数，有且只有一次
     
     }
 protected://保护访问权限，类内可以访问，类外不可以访问，子类继承后可以访问。
     int s_score;
 private://私有权限，类内可以访问，类外不可以访问，子类继承后不可访问。
     int s_id;
 };
 
 Student s1;
 showMessage();//可以访问
 showScore();//不能访问
 showId();//不能访问
 Student s2(s1);//拷贝构造
 ```
 构造函数的分类：1.按参数分为有参构造和无参构造。2.普通构造和拷贝构造。
 
 调用方法：1.括号法。2.显示法。3.隐式转换法。
 
 括号法：
 ```
 Student s1;//默认构造函数
 Student s1(10);//带参数的构造函数
 Student s2(s1);//拷贝构造函数
 //不能写成Student s1(); 与函数声明有二义性
 ```
 显示法:
 ```
 Student s1 = Student(10);//有参   等号右侧是匿名对象,它的特点是该语句执行后立即析构。不能用匿名对象进行拷贝构造。
 Student s2 = Student(s1);//拷贝
 ```
 隐式转换法：
 ```
 Student s1 = 10;//等价于Student s1 = Student(10);
 Student s2 = s1;//等价于Student s2 = Student(s1);
 ```
 
 拷贝构造函数的调用时机:
 (1)用创建完毕的对象初始化新对象`Student s2 = Student(s1);` 
 (2)值传递传参`void test(Student s) {}` 注意，这个参数s是临时副本(匿名对象)，不会影响调用语句`test(s1)`中s1的值。
 (3)返回局部对象的值
 ```
 void test(){
     Student s1;
     return s1;//返回时调用拷贝构造函数
 }
 ```
 
 编译器默认添加的函数：默认无参构造函数，默认析构函数，默认拷贝构造函数。如果定义了有参构造函数，就不提供无参构造函数，如果定义了拷贝构造，就不提供默认拷贝。
 
 ### 深拷贝与浅拷贝
 
 浅拷贝：简单赋值拷贝操作。(编译器的默认拷贝函数)  浅拷贝的问题：堆区重复操作。
 
 深拷贝：在堆区重新申请空间。 如果属性中有在堆区开辟的操作(手动分配内存)，就要用深拷贝。
 ```
 Student(const Student &s){
     s_age=s.s_age;
     s_name=s.s_name;
     ...
     //s_height=s.s_height; 浅拷贝
     s_height=new int(*s.s_height);//深拷贝，重新分配内存
 };
 ```
 
 ### 初始化列表
 ```
 Student(): s_name(123),s_age(18),s_uid(1234556){//构造函数名: 属性1(值1)，属性2(值2)...{函数体}
 
 }
 //更灵活的操作
 Student(int name,int age,int uid):s_name(name),s_age(age),s_uid(uid){}
 ```
 
 ### 类对象作为类成员
 
 一个类的对象可以作为另一个类的成员。
 ```
 class A{
 public:
     int a;
     int b;
     A(int a1,int b1): a(a1),b(b1){cout << 'a' << endl;}
 };
 
 class B{
 public:
     int a;
     int b;
     A c;
     B(int a1,int b1,const A &c1): a(a1), b(b1), c(c1){cout << 'b' << endl;}
 };
 B x;//当构造B的对象时，会先调用A的构造函数，再调用B的。
 //输出：a b
 ```
 
 ### 静态成员
 
 静态成员变量：
 1.所有对象共享同一份数据。(同一个存储空间)
 2.在编译阶段分配内存。(程序运行前分配在全局区)
 3.类内声明，类外初始化。
 
 静态成员函数：
 1.所有对象共享同一份数据。
 2.静态成员函数只能访问静态成员变量。
 
 ```
 class A{
 public:
     static int a;
     int b;
     static void test(){
         cout << a << endl;
         b=100;//报错，不能访问非静态成员变量
     }
 };
 int A::a = 1;//类外初始化
 //静态成员变量的两种访问方式
 A x;
 x.a;//通过对象访问
 A:a;//通过类名访问
 //静态成员函数的两种访问方式
 x.test();//通过对象
 A:test();//通过类名
 ```
 
 ## C++对象模型和this指针
 
 ### 成员变量和成员函数是分开存储的
```
 class A{
 public:
     int x;
     static int y;
     void test(){}
 };
 A a;
 //sizeof(a)大小计算：4
 /* 空类对象的大小至少是1。
 静态成员变量大小不计入类大小(因为存放在全局区)
 成员函数大小不计入(因为成员变量和成员函数是分开存储的)
 */
```
结论: 只有非静态成员变量属于类的对象上。

### this指针详解

this指针指向被调用的成员函数所属对象，本质上是指针常量，指针的指向不可修改。

用途：1.当形参和成员变量同名时，可以用this指针区分。2.在类的非静态成员函数中可以返回自身`return *this;`。3.可以进行链式编程。
```
 class A{
 public:
     int x;
     static int y;
     void add(int x){
         this->x+=x;
     }
     A& test(A &a){
         this->x=a.x;
         return *this;
     }
 };
```

### 空指针访问成员函数
```
 class A{
 public:
     int x;
     void showX(){
         //为了代码健壮性，这里应该判断this指针是否为空
         cout << x << endl;
     }
     void nothing(){}
 };
 test(){
     A *a = NNLL;
     a->showX();//报错，showX()访问了x，实际为this->x，此处this是空指针。
     a->nothing();//可以正常执行
 }
 int main(){
     test();
     return 0;
 }
```

### const修饰成员
```
class A{
 public:
     const int x;
     int y;
     mutable int z;
     void test() const{
         y=10;//报错，这里的const修饰this，将A* const this修改为const A* const this
         z=10;//不报错。mutable声明可以使常函数、常对象中属性仍可修改。
     }
 };
 //常对象只能调用常函数
 int main(){
     const A a;
     a.test();
     return 0;
 }

```

## 友元

三种实现:1.全局函数作友元。2.类作友元。3.成员函数作友元。

```
void test(A* a){//全局函数
    cout << a.x << endl;
    cout << a.y << endl;
}
class B;//先声明防止报错
class C;
class A{
    friend void test(A* a);//声明为友元,s使得该函数可以访问私有成员
    friend class B;//友元类
    friend void C::visit();//成员函数作友元
 public:
     int x;
     A(){
         x=1;
         y=2;
     }
 private:
     int y;
 };
 
 class B{
 public:
     A a;
     void visit(){
         cout << a.y << endl;//可以访问A类私有成员
     }
 };
 
 class C{
 public:
     void visit{
         cout << a.y << endl;//可以访问A类私有成员
     }
 };
 
 int main(){
     A a1;
     test(&a1);
 }
```

## 运算符重载

可以通过成员函数或全局函数实现重载。
```
/*
A operator+(A &a1,A &a2){
     A temp;
     temp.x = a2.x + a1.x;
     temp.y = a2.y + a1.y;
     return temp;
}
*/
//只能用全局函数重载左移运算符,实现自定义输出类型
ostream& operator<<(ostream &cout,A &a){
    cout << a.x << endl << a.y << endl;
    return cout;//满足链式编程
}
class A{
friend ostream& operator<<(ostream &cout,A &a);
public:
    int x;
    int y;
    A operator+(A &a1){
        A temp;
        temp.x = this->x + a1.x;
        temp.y = this->y + a1.y;
        return temp;
    }
    //运算符重载也可以发生函数重载
    A operator+(int n){
        A temp;
        temp.x = this->x + n;
        temp.y = this->y + n;
        return temp;
    }
   
};
void test(){
    A a1,a2,a3;
    a1.x=1;
    a1.y=2;
    a2.x=1;
    a2.y=2;
    a3=a1+a2;
    //成员函数重载本质上是 a3 = a1.operator+(a2);
    //全局函数重载本质上是 a3 = operator+(a1,a2);
    cout << a1;//打印1 2
}
```

## 继承
### 继承权限

```
class base{
public:
    int a;
protected:
    int b;
private:
    int c;//父类的私有属性，子类无论如何都不能访问
};

class A : public base{//公有继承，父类的公有和保护保持不变
public:
    void test(){
        cout << a << endl;
        cout << b << endl;
        //cout << c << endl;//不可访问
    }
protected:

private:
};

class B : protected base{//保护继承，父类的公有和保护均变为保护
public:
    void test(){
        cout << a << endl;
        cout << b << endl;
        //cout << c << endl;//不可访问
    }
};

class C : private base{//私有继承，父类的公有和保护均变为私有
public:
    void test(){
        cout << a << endl;
        cout << b << endl;
        //cout << c << endl;//不可访问
    }
};

void test01(){
    A.a=10;
    A.b=10;//保护权限,不可访问
    A.c=10;//私有权限,不可访问
    B.a=10;//保护权限,不可访问
    B.b=10;//保护权限,不可访问
    B.c=10;//私有权限,不可访问
    C.a=10;//私有权限,不可访问
    C.b=10;//私有权限,不可访问
    C.c=10;//私有权限,不可访问
}
```

### 继承中的sizeof()问题
```
class base{
public:
    int a;
protected:
    int b;
private:
    int c;
};

class A : public base{
public:
    int d;
};

void test(){
    A a;
    cout << sizeof() << endl;//输出16，子类继承父类所有非静态成员属性(包括私有)
}
```

### 继承中的构造和析构顺序
```
class base{
public:
    base(){
        cout << "base构造" << endl;
    }
    ~base(){
        cout << "base析构" << endl;
    }
};

class A : public base{
public:
    A(){
        cout << "A构造" << endl;
    }
    ~A(){
        cout << "A析构" << endl;
    }
};

void test(){
    A a;//输出内容:base构造 A构造 A析构 base析构
    
}
```

### 继承同名成员处理方式

子类与父类有同名成员时，隐藏父类成员。子类成员直接访问，父类成员加作用域访问。静态成员也一样。
```
class base{
public:
    int x;
    static int y;
    void func(){
        cout << "base" << endl;
    }
    base(){
        x=1;
    }
};
class A : public base{
public:
    int x;
    static int y;
    void func(){
        cout << "A" << endl;
    }
    A(){
        x=2;
    }
};
int base::y = 1;
int A::y = 2;
void test(){
    A a;
    cout << a.x << endl;//输出2 可以直接访问子类同名成员
    cout << a.base::x << endl;//输出1  加作用域，即可通过子类对象访问父类同名成员
    a.func();//打印A  直接调用，调用的是子类同名成员函数
    a.base::func();//打印base  同上
    //静态成员通过对象访问，同上
    cout << a.y << endl;//输出2
    cout << a.base::y << endl;//输出1
    //静态成员通过类名访问
    cout << A::y << endl;//输出2
    cout << A::base::y << endl;//输出1  第一个::表示通过类名访问，第二个::表示父类作用域
    cout << base::y << endl;//输出1
}
```

### 多继承
```
class Base1{
public:
    int a;
    int b;
    Base1(){
        a=1;
    }
};
class Base2{
public:
    int a;
    int c;
    Base2(){
        a=2;
    }
};
class Son : public Base1,public Base2{
public:
    int e;
};
void test(){
    Son s;
    cout << sizeof(s) << endl;//打印20
    cout << s.a << endl;//报错，产生二义性
    cout << s.Base1::a << endl;//打印1  加作用域区分同名
}
```

### 菱形继承问题

菱形继承时，若两个父类有同名成员，必须加作用域区分。

菱形继承导继承了两份相同资源，造成了浪费。

利用虚继承可以解决菱形继承问题。
```
class Base{
public:
    int a;
};
class A : public Base{
    
};
class B : public Base{

};
class C : public A,public B{

};
void test(){
    C c;
    c.a = 10;//报错，不明确
    c.A::a = 10;
    c.B::a = 20;
    cout << c.A::a << endl;//10
    cout << c.B::a << endl;//20
}
```

### 虚继承
```
class Base{
public:
    int a;
};
class A : virtual public Base{
    //A中有虚基类指针，指向虚基类表，虚基类表中存放了数据的偏移量,指针指向的地址加偏移量就得到数据地址
};
class B : virtual public Base{
    
};
class C : public A,public B{

};
void test(){
    C c;
    c.a = 10;//不再报错,C类中只有一分数据
    c.A::a = 10;
    c.B::a = 20;
    cout << c.A::a << endl;//20
    cout << c.B::a << endl;//20
}
```

## 多态
多态分为静态多态和动态多态

静态多态：函数重载和运算符重载。 静态多态在编译阶段确定函数地址。

动态多态：派生类和虚函数实现运行时多态。 动态多态在运行阶段确定函数地址。
```
class Base{
public:
    void func(){
        cout << "Base" << endl;
    }
    virtual void func01(){
        cout << "Base" << endl;
    }
};
class A : public Base{
public:
    void func(){
        cout << "A" << endl;
    }
    void func01*({
        cout << "A" << endl;
    }
};
void test01(Base &base){
    base.func();
}
void test02(){
    A a;
    test01(a);//C++允许父子之间的类型转换，这里自动将子类对象转换为父类对象
}
void test03{
    base.func01();
}
void test04(){
    A a;
    test03(a);
}
int main(){
    test02();//打印base 原因：因为函数地址在编译阶段绑定
    test04();//打印A    原因：因为函数地址在运行阶段绑定
    Base base;
    cout << sizeof(base) << endl;//打印4 ，4个字节表示指针，即虚函数指针
    return 0;
}
```
动态多态条件:1.存在继承关系。2.子类重写父类虚函数(子类中virtual可写可不写，函数参数必须完全一致)。

动态多态使用:父类的指针或引用指向子类对象。

动态多态原理:子类重写父类虚函数时，子类的虚函数表中，子类函数地址覆盖父类函数地址。

多态的优点：1.代码组织结构清晰。2.可读性强。3.利于扩展维护。

## 纯虚函数和抽象类
有纯虚函数的类是抽象类

抽象类特点:1.无法实例化对象。2.子类必须重写父类中纯虚函数，否则也是抽象类。
```
class Base{
public:
    virtual void func() = 0;//纯虚函数
};
class A : public Base{
public:
    void func(){
        cout << "A" << endl;
    }
};
int main(){
    Base *base = new A;//父类的指针或引用指向子类接口，实现调用
    base->func();//打印A
    return 0;
}
```
## 虚析构和纯虚析构
使用多态时，如果子类有属性开辟到堆区，则父类指针无法调用子类的析构代码。

解决方法：将父类的析构函数改为虚析构或纯虚析构。

虚析构和纯虚析构共性：可以解决父类指针释放子类对象问题；要有具体函数实现。
```
class Base{
public:
    virtual void func() = 0;//纯虚函数
    
    //~Base(){
    //    cout << "Base析构" << endl;
    //}
    
    virtual ~Base(){//虚析构
        cout << "Base虚析构" << endl;
    }
    //virtual ~Base() = 0;//纯虚析构
};
//如果父类中只声明纯虚析构而没有定义，会报错(删除父类指针时，显然要调用父类析构)
//可以在类外定义
Base::~Base(){
    cout << "Base纯虚析构" << endl;
}

class A : public Base{
public:
    string *name;//堆区数据
    void func(){
        cout << "A" << endl;
    }
    A(string n){
        name = new string(n);
    }
    ~A(){
        if(name!=NULL)
        {
            cout << "A析构" << endl;
            delete name;
            name = NULL;
        }
    }
};
int main(){
    Base *base = new A;
    base->func();
    delete base;//输出内容：Base析构
    //没有调用子类析构函数，因为父类指针删除不会调用子类析构函数，造成内存泄漏
    //解决方案：父类声明为虚析构或纯虚析构
    return 0;
}
```

# 文件操作
头文件<fstream>

三大类：

1.ofstream：写操作
 ```
 #include <fstream>
 
 ofstream ofs;//创建对象
 
 ofs.open("path",mod);//ios::in 读打开 ios::out 写打开 ios::binary 二进制方式 ios::app 追加写 
 //例：ofs.open("path",ios::in | ios::binary);//以二进制写打开
 
 ofs.is_open();//成功返回1
 
 ofs << "写入";
 
 ofs.close();
 ```

2.ifstream：读操作
 
 四种读取方式
 ```
 //前三种按行读，第四种按字符读
 //1.
 char buf[1024]={0};
 while(ifs >> buf){
     cout << buf << endl;
 }
 //2.
 char buf[1024]={0};
 while(ifs.getline(buf,sizeof(buf))){
     cout << buf << endl;
 }
 //3.
 string buf;
 while(getline(ifs,buf)){
     cout << buf << endl;
 }
 //4. (不推荐)
 char c;
 while((c=ifs.get())!=EOF){
     cout << c << endl;
 }
 ```
3.fstream：读写操作(略)

# 模板
 作用：提高复用性。将类型参数化。
 
 ```
 template<typename T>//声明一个模板，T是通用数据类型
 void mySwap(T &a,T &b){
     T temp = a;
     a = b;
     b = temp;
 }
 
 int main(){
     //模板使用方法
     int a = 1;
     int b = 2;
     mySwap(a,b);//编译器自动推导
     mySwap<int>(a,b);//显式指定类型
     return 0;
 }
 
 ```
 
 注意事项：1.自动类型推导必须推导出一样的数据类型。2.模板必须确定T的数据类型。
 ```
 template<class T>
 void mySwap(T &a,T &b){
     T temp = a;
     a = b;
     b = temp;
 }
 template<class T>
 void test(){
     cout << "test" << endl;
 }
 int main(){
     int a = 1;
     char b = 'a';
     mySwap(a,b);//错误，无法推导T类型
     test();//错误，无法确定T的数据类型，必须显式调用
     test<int>();//正确
     return 0;
 }
 ```
 ### 普通函数与函数模板的区别：能否发生隐式类型转换。
 
 1.普通函数可以发生隐式类型转换。
 
 2.函数模板采用自动类型推导，不能发生隐式类型转换。
 
 3.函数模板采用显式类型，可以发生隐式类型转换。
 ```
 template<class T>
 void mySwap(T &a,T &b){
     T temp = a;
     a = b;
     b = temp;
 }
  int main(){
     int a = 1;
     char b = 'a';
     mySwap<int>(a,b);//发生隐式类型转换，'a'转换为97
     return 0;
 }
 ```
 
 ### 调用规则
 
 1.普通函数和函数模板都可以调用时，优先普通函数。
 
 2.空模板参数列表强制调用函数模板。
 
 3.函数模板可以重载。
 
 4.如果函数模板可以产生更好的匹配，优先调用函数模板。
 
 ```
 void test(int a,int b){
     cout << "普通函数" << endl;
 }
 template<class T>
 void test(T a,T b){//发生重载
     cout << "函数模板" << endl;
 }
 template<class T>
 void test(T a,T b,T c){//发生重载
     cout << "函数模板重载" << endl;
 }
  int main(){
     int a = 1;
     int b = 2;
     test(a,b);//调用普通函数  假如普通函数只有声明，这里不会调用模板，而是报错。想调用模板，要使用空模板参数列表。
     test<>(a,b);//强制调用函数模板
     test(a,b,100);//调用重载函数模板
     char x = 'a';
     char y = 'b';
     test(a,b);//调用函数模板  更好的匹配
     return 0;
 }
 ```
 
 模板的局限性：不万能，对某些特殊的类型不通用，需要对特定数据类型提供具体操作。
 ```
 template<class T>
 void test(T a,T b){
     cout << a+b << endl;
 }
 
 int main(){
     int a = 1;
     int b = 2;
     test(a,b);//可以执行
     int c[10]={0};
     int d[10]={0};
     test(c,d);//不能执行
 }
 ```
 
 ## 类模板
 ```
 template<class NameType,class AgeType>
 class A{
 public:
     NameType name;
     AgeType age;
     A(NameType name,AgeType age){
         this->name = name;
         this->age = age;
     }
     void show(){
         cout << this->name << this->age << endl;
     }
 };
 
 int main(){
     A<string,int> a("hk416",24);//注意理解“类型参数化”这个概念
     a.show();
     return 0;
 }
 ```
