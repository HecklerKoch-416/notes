# c++学习笔记
1.引用：引用的本质是取别名。
 
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
 
 2.函数高级：
 
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
 
 3.类和对象
 
 C++面向对象三大特性：封装、继承、多态
 
 封装：将属性和行为作为整体；权限控制。
 
 class与struct区别：默认访问权限不同，class默认是私有权限,struct默认是公共权限。
 
 成员属性私有化的作用：1.控制读写权限；2.在写时可以检测数据有效性。//只能由成员函数修改私有属性
 
 构造函数与析构函数：构造函数负责初始化，析构函数负责清理。如果没有手动定义，编译器会自动生成构造函数和析构函数。
 构造函数和析构函数由编译器调用，不能由用户调用。
 
 ```
 class Student{
 public://公共访问权限，均可访问。
     string s_name;
     unsigned int s_age;
     int s_uid;
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
 protected://保护访问权限，类内可以访问，类外不可以访问，子类继承后可以访问。
     int s_score;
 private://私有权限，类内可以访问，类外不可以访问，子类继承后不可访问。
     int s_id;
 };
 
 Student s1;
 showMessage();//可以访问
 showScore();//不能访问
 showId();//不能访问
 ```
  
  

  
