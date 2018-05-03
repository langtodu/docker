#### yaml简介

yaml是一个常被用来编写配置文件的语言，功能类似于json，但相比于json，可读写强，使用起来简介方便。

##### yaml语法规则

-   对大小写敏感
-   使用缩进表示层级关系，一般使用空格缩进
-   同层级元素左侧对齐
-   如果使用冒号，冒号与后面内容之间加空格
-   注释使用 **#**

##### yaml支持的数据结构
-   对象：key-value集合；冒号后必须加空格
```
role: master 
等价于：{role:master}
```
-   数组：一组按次序排列的值；
```
-   master
-   salve
等价于：[master,salve]
```
-   复合结构：将对象和数组结合使用；
```
role:
  - master
  - salve
等价于：{role：['master','salve']}
```
-   纯量（scalars）：单个不可分割的值，如数值、字符串、时间、日期、布尔值等；
    -   数值
    ```
    number： 100    #等价于{number:100}
    ```
    -   时间
    ```
    date: 2008-01-01 #等价于{date: Tue Jan 01 2008 08:00:00 GMT+0800 (中国标准时间)}
    ```
    -   null(null用~表示)
    ```
    parent: ~        #等价于{parent:null}
    ```
    -   两个!!强制转换数据类型
    ```
    a: !!str 123
    b: !!str true
    c: !!int '123'
    #等价于{a:'123', b:'true', c:123}
    ```
    -   字符串（默认不加引号,也可以使用单引号或双引号，但双引号不会转义字符）
    ```
    # 字符串可以写成多行，但第二行开始必须用空格缩进。换行符会被替换成空格
    str: this
      is a
      test
    #等价于{str:'this is a test'}
    str: hello    #等价于{str:'hello'}
    # 可以使用 | 保留换行符
    this: |
      foo
      bar
    #等价于{this:'foo\nbar\n'}
    # 可以使用 > 折叠换行
    this: >
      foo
      bar
    # 等价于{this:'foo bar\n'}
    ```

参考：

[json-yaml转换网站](https://www.bejson.com/json/json2yaml/)

http://www.ruanyifeng.com/blog/2016/07/yaml.html?f=tt

https://en.wikipedia.org/wiki/YAML