# c++学习笔记
 1.引用：引用的本质是取别名。
 
 引用必须初始化，引用初始化后不可更改。
 
 引用作为函数参数：与地址作为参数起一样的作用。
 
 引用作为函数返回值：不能返回局部变量的引用；函数返回引用时可以作为左值。
 
  `int &test(){`
  
  `static int a = 1;`
  
  `return a;`
  `}`
  
  `int main(){`
  
  `int ref=test();`
  
  `cout << ref << endl;`
  
  `test()=2;//test()是返回值a的引用，修改test()即修改a`
  
  `cout << ref << endl;//输出结果为2`
  
  `}`
  
