---
title: C++考点背诵
date: 2025-01-02 16:10:30
tags:
  - cpp
  - 期末考试
---
1. 符号优先级（**左**结合）
- 1元
- 乘除模
- 加减
- 移动运算符
- 关系运算符
- &&
- ||
- 三目运算符
- 赋值运算符
- 逗号运算符
2. 类型转换
- 隐式转换：精度丢失、溢出
- static_cast：直接类型转换、更强的类型检查、基本类型
- const_cast：只去除**指针**，修改常量产生未定义行为 
- dynamic_cast：类结构指针转换失败抛出nullptr，引用失败会产生std::badcast

补充：const问题，变量初始对象是否为const决定是否能修改。
3. union和struct
- union：共享内存
- struct == public class、对齐

结构体对齐算法
```python
def alignment(struct, default, cpu_digits):
    # 确定对齐方式
    if default is not None:
        align = default
    else:
        align = max_member_size(struct) if max_member_size(struct) > cpu_digits else cpu_digits
    offset = 0
    for member in struct:
        # 计算当前成员的对齐偏移
        member_size = size_of(member)
        member_alignment = min(align, member_size)
        # 计算填充
        if offset % member_alignment != 0:
            padding = member_alignment - (offset % member_alignment)
            offset += padding
        # 放置成员
        place(offset, member)
        offset += member_size
    # 计算结构体的总大小，考虑最终对齐
    if offset % align != 0:
        offset += align - (offset % align)

    return offset  
```
4. 函数返回值
- tuple：多个返回值
- std::optional\<T\>：可以返回nullopt或者一个T实例
- variant：类型安全的union
- any：类型安全的void*
5. 数组指针综合
- 按照括号从里向外分析，如果一层括号呈现'\(\* xxxx\)'的形式，那么就将这个括号右半部分和左括号处于同一括号内层的所有部分移动到其对应的'\(\*'部分
- 重复以上过程直至每个括号里面只有'\*'，然后从左向右按照这样的范式依次合并：'Type(\*)\[n\]'代表Type的指针（的n维数组），将所有'Type(\*)\[n\]'替换为一个新的Type，其值为'Type的指针的n维数组'，直到不出现指针为止
6. 指针与空间分配
- new delete：如果在new使用[]，则在delete也要使用[]
- unique_ptr（不能转染所有权，只能用std::move来移动）：生命周期调用析构函数
- shared_ptr（能够转让所有权）：最后一个持有对象的shared_ptr被销毁或者被赋值为另外一个指针的时候对象会销毁(.get()读取原来的指针，使用use_count()来查看当前对象的引用计数)
- weak_ptr（没有计数器，是「丐版」shared_ptr）：只能通过weak_ptr或者shared_ptr进行构造，通过.lock().get()来访问（lock返回一个shared_ptr对象） 
7. 函数一般的执行过程
- 将参数或者参数地址压栈
- 将返回地址压栈，保持调用者的现场（ebp、esp）
- 跳转到函数入口地址
- 设置新函数的ebp
- 分配局部变量空间
- 执行函数体
- 释放局部变量空间
- 加载调用者的ebp，跳转并弹出返回地址
- 重新设置调用者的esp
- 返回调用者，继续执行
8. 内联函数
编译器可以选择**展开**或者**不展开**（较复杂或者出现递归）
9. 多文件组织
- extern:外部的函数和变量（编译器看不到外部文件的变量，执行在链接阶段）
- static: 可以作用于函数和变量，限制作用域于**当前文件**的**全局**（参考java中的static）
10. lambda表达式的捕获
- \[=\]以值的方式捕获所有外部变量
- \[&\]以引用的方式捕获外部的所有变量
- \[this\]捕获当前类对象的this指针
- \[a,&b\]捕获a的value和b的引用
11. const和mutable
- 函数修饰为const的函数不能修饰非mutable的变量
- 对象被设置为const，那么其**所有成员变量**则不可以修改
12. 移动构造和拷贝构造函数
- &：左值引用：拷贝构造函数
- &&：右值引用：移动构造函数
13. 虚函数
- 「虚函数」属性可以向下传递
- 静、内、构不可能是
- 派生类申请资源析构必须实例化
- 虚函数完成构造才会绑定
- override声明重写，final防止进一步重载
- 虚函数有默认实现
- 纯虚函数后面加上=0意思为delete
14. 初始化顺序
- 调用父类初始化函数
- 调用子类初始化函数
15. 多态的三种方式
- 函数重载
- 模板
- 虚函数
- 操作符重载
16. 操作符重载
- 自增运算符后缀使用哑运算符，并且返回值
- 赋值运算符要判断是否自我赋值
- 一般格式 \<Return_Type\> operator \<OP\> \(\<FunctionFParams\>\){\<Expressions\>}
- 括号重载运算符
- 成员重载运算符（类包装器）
17. Resource Acquisition Is Initialization（RAII）
- 构造函数请求资源，建立所有类不变式，但是在无法完成的时候抛出异常
- 析构函数释放资源但是绝不抛出异常
- 自身拥有自动储存期或者临时生存期
- 有与自动或者临时对象的生存期绑定的生存期
18. 异常处理
- 调用链
- 先子类后父类
- 没有finally怎么办？异常处理后一定会调用对象的析构函数（对应RAII的第二点，所以析构函数要求禁止抛出任何异常）