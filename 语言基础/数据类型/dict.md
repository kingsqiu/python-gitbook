# dict：映射类型。 俗称-字典

 [mapping](https://docs.python.org/zh-cn/3.7/glossary.html#term-mapping) 对象会将 [hashable](https://docs.python.org/zh-cn/3.7/glossary.html#term-hashable) 值映射到任意对象。 映射属于可变对象。 目前仅有一种标准映射类型 *字典*。  

 字典显示是一个用花括号括起来的可能为空的键/数据对系列: 

```
dict_display       ::=  "{" [key_datum_list | dict_comprehension] "}"
key_datum_list     ::=  key_datum ("," key_datum)* [","]
key_datum          ::=  expression ":" expression | "**" or_expr
dict_comprehension ::=  expression ":" expression comp_for
```

根据官方的语法标注可知：

1. 字典用"{}"进行字典的表示
2. 字典可为空
3. 字典可包含字典
4. 字典的值可以用"**"进行字典拆包
5. 字典中的数据用":"表示映射关系。
6. 字典中用","对单个数据进行分离

