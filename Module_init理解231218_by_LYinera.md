### Module_init为什么可容易定义入口函数？

在include/linux/module.h中有Module_init的宏定义，当定义该驱动为模块(ko文件)时，Module_init函数的作用是将init_module函数的别名命名为Module_init所定义的函数。

```c
int init_module(void) __attribute__((alias(#fn)));
```

init_module是所有module驱动的入口函数！！！

如果要将驱动编译进内核，Module_init不再定义init_module函数，而是变为\_\_initcall函数，其效果是定义一个名为\_\_initcall\_##fn##id的结构体，该结构体的指针为驱动的init函数，其有一个段属性为\_\_initcall#id.init，因此不会发生重复

```c
static initcall_t __initcall_##fn##id __used \
__attribute__((__setion__("__initcall"#id".init"))) = fn
```