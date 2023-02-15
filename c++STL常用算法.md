# 常用遍历算法
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
//其中first是容器开头的迭代器，last是容器结尾的迭代器，f是仿函数/函数
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
                   UnaryOperation unary_op)//第一个容器的起始迭代器，第一个容器的结束迭代器，第二个容器的起始迭代器，仿函数/函数
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
# 常用查找算法
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
  if(it == v.end())
    cout << "查找失败" << endl;
   else
    cout << "查找成功" << it->name << " " << it->age << endl;
}
```

## find_if
按条件查找

原型
```
template<class InputIt, class UnaryPredicate>
InputIt find_if(InputIt first, InputIt last, UnaryPredicate p)//起始迭代器，结束迭代器，bool类型函数/谓词
{
    for (; first != last; ++first) {
        if (p(*first)) {
            return first;
        }
    }
    return last;
}
```

用法
```
//内置数据类型省略
//只给出自定义数据类型
class Person{
public:
    string name;
    int age;
    Person(string a,int b){
        name = a;
        age = b;
    }
};

//仿函数
class Greater20{
    bool operator()(Person &p){
        if(p.age>20)
            return true;
        return false;
    }
}

void test(){
  vector<Person> v;
  for(int i=0;i<30;i++)
    v.push_back(i);
  vector<int> iterator it = find(v.begin(),v.end(),Greater20());
  if(it == v.end())
    cout << "查找失败" << endl;
   else
    cout << "查找成功" << it->name << " " << it->age << endl;
}
```
## adjacent_find
查找相邻重复元素

原型
```
template<class ForwardIt>
ForwardIt adjacent_find(ForwardIt first, ForwardIt last)//起始迭代器，结束迭代器，返回第一个重复元素的迭代器/结束迭代器
{
    if (first == last) {
        return last;
    }
    ForwardIt next = first;
    ++next;
    for (; next != last; ++next, ++first) {
        if (*first == *next) {
            return first;
        }
    }
    return last;
}
```

## binary_search
查找元素是否存在(二分查找,必须在有序序列中查找)

原型
```
template<class ForwardIt, class T>
bool binary_search(ForwardIt first, ForwardIt last, const T& value)
{
    first = std::lower_bound(first, last, value);//loewer_bond 返回指向首个不小于 value 的元素的迭代器，或若找不到这种元素则为 last 。
    return (!(first == last) && !(value < *first));
}
```

## count
统计元素出现个数

原型
```
template<class InputIt, class T>
typename iterator_traits<InputIt>::difference_type
    count(InputIt first, InputIt last, const T& value)
{
    typename iterator_traits<InputIt>::difference_type ret = 0;
    for (; first != last; ++first) {
        if (*first == value) {      //对于自定义数据类型，要重载==运算符
            ret++;
        }
    }
    return ret;
}
```

## count_if
条件查找

原型
```
template<class InputIt, class UnaryPredicate>
typename iterator_traits<InputIt>::difference_type
    count_if(InputIt first, InputIt last, UnaryPredicate p)
{
    typename iterator_traits<InputIt>::difference_type ret = 0;
    for (; first != last; ++first) {
        if (p(*first)) {
            ret++;
        }
    }
    return ret;
}
```

# 常用搜索算法
## sort

原型
```
 template<typename _RandomAccessIterator, typename _Compare>
    inline void
    __sort(_RandomAccessIterator __first, _RandomAccessIterator __last,
	   _Compare __comp)//第三个参数可填可不填，默认升序，可利用谓词自行修改
    {
      if (__first != __last)
	{
	  std::__introsort_loop(__first, __last,
				std::__lg(__last - __first) * 2,
				__comp);
	  std::__final_insertion_sort(__first, __last, __comp);
	}
    }
```
在functional头文件中有greater<T>仿函数
```
vecrot<int> v;
//...
sort(v.begin(),v.end(),greater<int>);//可实现降序排列    
```

## random_shuffle
随机打乱

```
//不提供仿函数接口
template< class RandomIt >
void random_shuffle( RandomIt first, RandomIt last )
{
    typename std::iterator_traits<RandomIt>::difference_type i, n;
    n = last - first;
    for (i = n-1; i > 0; --i) {
        using std::swap;
        swap(first[i], first[std::rand() % (i+1)]);//想要每次打乱顺序都不同，用srand()提供种子
        // rand() % (i+1) 实际上不准确，因为生成的数对于多数 i 值不均匀分布。
        // 正确实现将实际上需要重新实现 C++11 std::uniform_distributtion ，
        // 这超出了此示例的范畴。
    }
}
//提供仿函数接口	
template<class RandomIt, class RandomFunc>
void random_shuffle(RandomIt first, RandomIt last, RandomFunc&& r)
{
    typename std::iterator_traits<RandomIt>::difference_type i, n;
    n = last - first;
    for (i = n-1; i > 0; --i) {
        using std::swap;
        swap(first[i], first[r(i+1)]);
    }
}	
```	
	
## merge
合并容器(有序)
```
template<class InputIt1, class InputIt2, class OutputIt>
OutputIt merge(InputIt1 first1, InputIt1 last1,
               InputIt2 first2, InputIt2 last2,
               OutputIt d_first)//第一个容器起始、结束，第二个容器起始、结束，目标容器起始迭代器
{				//要提前给目标容器分配足够的存储空间
    for (; first1 != last1; ++d_first) {
        if (first2 == last2) {
            return std::copy(first1, last1, d_first);
        }
        if (*first2 < *first1) {
            *d_first = *first2;
            ++first2;
        } else {
            *d_first = *first1;
            ++first1;
        }
    }
    return std::copy(first2, last2, d_first);
}	
```

## reverse
反转

```			      
  template<typename _BidirectionalIterator>
    inline void
    reverse(_BidirectionalIterator __first, _BidirectionalIterator __last)
    {
      // concept requirements
      __glibcxx_function_requires(_Mutable_BidirectionalIteratorConcept<
				  _BidirectionalIterator>)
      __glibcxx_requires_valid_range(__first, __last);
      std::__reverse(__first, __last, std::__iterator_category(__first));
    }
```
			      
# 常用拷贝和替换算法
## copy
```
template<class InputIt, class OutputIt>
OutputIt copy(InputIt first, InputIt last, 
              OutputIt d_first)	//提前开辟空间
{
    while (first != last) {
        *d_first++ = *first++;
    }
    return d_first;
}	
```
## copy_if
```
template<class InputIt, class OutputIt, class UnaryPredicate>
OutputIt copy_if(InputIt first, InputIt last, 
                 OutputIt d_first, UnaryPredicate pred)//提前开辟空间
{
    while (first != last) {
        if (pred(*first))
            *d_first++ = *first;
        ++first;
    }
    return d_first;
}	
```
## replace
```
template<class ForwardIt, class T>
void replace(ForwardIt first, ForwardIt last,
             const T& old_value, const T& new_value)
{
    for (; first != last; ++first) {
        if (*first == old_value) {//注意重载运算符
            *first = new_value;
        }
    }
}	
```
## replace_if
```
template<class ForwardIt, class UnaryPredicate, class T>
void replace_if(ForwardIt first, ForwardIt last,
                UnaryPredicate p, const T& new_value)
{
    for (; first != last; ++first) {
        if(p(*first)) {
            *first = new_value;
        }
    }
}	
```
## swap
交换两个容器
	
```
template< class T >
constexpr void swap( T& a, T& b ) noexcept(/* see below */);	
//该关键字告诉编译器，函数中不会发生异常,这有利于编译器对程序做更多的优化。
```
