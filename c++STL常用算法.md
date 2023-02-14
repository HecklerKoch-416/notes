# 遍历
常用头文件
```
#include<algorithm>
#include<numeric>
#include<functional>
```
## for_each
遍历容器

原型
```
template<class InputIt, class UnaryFunction>
UnaryFunction for_each(InputIt first, InputIt last, UnaryFunction f)
{
    for (; first != last; ++first) {
        f(*first);
    }
    return f; // C++11 起隐式移动
}
//其中first是容器开头的迭代器，last是容器结尾的迭代器，f是仿函数
```
用法
```
class myFunc{
public:
  void operator()(int i){
    cout << i << nedl;
  }
}

void test(){
  vector<int> v;
  //对v赋值省略
  for_each(v.begin(),v.end(),myFunc());
}
```

## transform
搬运容器

原型
```
template<class InputIt, class OutputIt, class UnaryOperation>
OutputIt transform(InputIt first1, InputIt last1, OutputIt d_first, 
                   UnaryOperation unary_op)//第一个容器的起始迭代器，第一个容器的结束迭代器，第二个容器的起始迭代器，仿函数
{
    while (first1 != last1) {
        *d_first++ = unary_op(*first1++);
    }
    return d_first;
}
```
用法
```
class myTran{
public:
  void operator()(int v){
    return v;
  }
}

void test(){
  vector<int> v1;
  for(int i=0;i<10;i++)
    v1.push_back(i);
  vector<int> v2;
  v2.resize(10);//注意v2容器大小必须大于等于v1
  transform(v1.begin(),v1.end(),v2.begin(),myTran());
}
```

## find
查找

原型
```
template<class InputIt, class T>
InputIt find(InputIt first, InputIt last, const T& value)//起始迭代器，结束迭代器，查找的元素，返回迭代器
{
    for (; first != last; ++first) {
        if (*first == value) {
            return first;
        }
    }
    return last;
}
```

用法
```
void test(){
  vector<int> v;
  int value = 5;
  for(int i=0;i<10;i++)
    v.push_back(i);
  vector<int> iterator it = find(v.begin(),v.end(),value);
}
```
问题：底层实现 `*first == value` 对于自定义数据类型，编译器无法识别
```
class Person{
public:
    string name;
    int age;
    Person(string a,int b){
        name = a;
        age = b;
    }
    bool operator==(const Person& p){//重载运算符==使得编译器能够识别
        if(this->name == p.name && this->age == p.age)
            return true;
           else
            return false;
    }
};

void test(){
  vector<Person> v;
  //容器加入数据忽略
  Person p("a",1);
  vector<int> iterator it = find(v.begin(),v.end(),p);
  //如果不重载==运算符，会报错
}
```
