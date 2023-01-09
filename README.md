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
 }
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
 }
 
 class B{
 public:
     int a;
     int b;
     A c;
     B(int a1,int b1,const A &c1): a(a1), b(b1), c(c1){cout << 'b' << endl;}
 }
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
 }
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
 }
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
 }
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
 }
 test(){
     A *a = NNLL;
     a->showX();//报错，showX()访问了x，实际为this->x，此处this是空指针。
     a->nothing();//可以正常执行
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
 }
 //常对象只能调用常函数
 const A a;
 a.test();
```



