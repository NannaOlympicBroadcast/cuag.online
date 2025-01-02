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