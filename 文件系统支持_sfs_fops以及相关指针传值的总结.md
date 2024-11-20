# 关于fops, sysfs, 私有结构体指针传值的总结

## 1. fops的定义及使用

### 1.1 fops的定义

fops是指一个file_operations结构体或一个指向包含该结构体的指针。是把系统调用和驱动程序关联起来的关键数据结构。通过创建虚拟设备节点，使用open，read，ioctl，write等函数访问驱动的方式。

### 1.2 fops的使用

#### 1.2.1 fops的‘创建’以及访问

在驱动层创建自己的open，read，ioctl，write方法，使用file_operation对系统函数与自己创建的函数进行关联。

**驱动层ioctl定义示例：**

```c
static long ioctl_hello (struct file *my_file,unsigned int cmd, long unsigned int arg){}
```

**驱动层ioctl使用file_operation关联示例：**

```c
static struct file_operations fops_hello=
{
	.ioctl = ioctl_hello,
}
```

在用户层调用相应的open，read，ioctl，write完成对ioctl函数的访问。

**用户层ioctl调用示例：**

```c
ioctl(fd, CMD_IOC_1, &ctlData);
```

#### 1.2.2 fops私有结构体的关联

首先，私有结构体中必须包含device等包含fops的结构体

在完成声明函数对应file_operation后，在结构体初始化完成设备注册时对设备信息fops进行赋值。

**私有结构体示例：**

```c
struct hello{
	struct miscdevice misc_hello;	//包含fops的结构体
}；
struct hello *p_hello；	//指向struct hello的指针
```

**赋值示例：**

```c
p_hello->misc_hello.fops = &fops_hello;
```



## 2. sysfs的定义及使用

## 2.1 sysfs的定义

sysfs是一个系统在启动时构建在内存中虚拟文件系统，一般被挂载在/sys目录下，掉电不保存，不能存储用户数据。作用是以文件形式将设备和驱动程序的信息导出到用户空间，方便了用户读取设备信息，同时支持修改和调整。

### 2.2 sysfs的使用

#### 2.2.1 sysfs的声明

定义用于访问sysfs文件的show、store函数（实际对应的是驱动ioctl模式下的read write接口，提供外部访问驱动的方法）

**show函数定义示例**

```
static ssize_t hello_show(struct kobject *kobj, struct kobj_attribute *attr,char *buf){}
```

使用attr完成对文件属性的初始化并添加至attribute数组，四个参数分别为文件名，文件权限，读文件方法，写文件方法，一个驱动可以同时对外提供多个文件，通过不同文件的show store函数，来完成复杂操作（例如: 创建hello_LHR文件，读取该文件时返回字符串hello lhr，写入该文件时，修改对应字符串）

**文件属性的声明及数组添加**

```c
sysfs_create_groupstatic struct kobj_attribute hello_attr = __ATTR(hello_LHR,0660,hello_show,hello_store);
// 注意，struct attribute *my_attrs[]结构体必须以NULL,作为结尾
struct attribute *my_attrs[] = {
    &hello_attr.attr,
    NULL,
}; 
```

#### 2.2.2 生成sysfs文件节点

**获得kobject指针**

```c
static struct kobject *g_Kob;
g_Kob = kobject_create_and_add("hello",kernel_kobj->parent)；
```

**使用sysfs_create_files或sysfs_create_group来生成文件节点**

使用sysfs_create_files和sysfs_create_group的区别是在kobject对应的目录里是否还有子目录，如果没有使用sysfs_create_files，如果有则使用sysfs_create_group

**使用sysfs_create_files：**

```c
sysfs_create_files(g_Kob,(const struct attribute **)my_attrs);
```

**使用sysfs_create_group：**

在使用sysfs_create_group时需要先完成attribute_group的定义,然后调用sysfs_create_group来添加。

**attribute_group的定义示例**

```c
struct attribute_group my_group = {
    .name     = "mygroup",	//子目录名称
    .attrs    = my_attrs,	//包含的attribute
};
```

**调用sysfs_create_group添加示例**

```c
sysfs_create_group(g_Kob, &my_group);
```



## 3. 通过传递私有结构体的指针完成值的传递

想要通过私有结构体完成数据的传递，私有结构体必须包含操作函数传进的参数类型的结构体，在函数中使用container_of函数来获得私有结构体的指针，进而获取所需要的数据。

**私有结构体的声明示例**

```
struct hello
{
	int a;							//所需数据
    struct kobject lhrKob;          
    struct kobj_attribute lhrAttr;	//用于sysfs与结构体关联
    struct miscdevice misc_hello;	//用于fops与结构体关联
};
```

**完成私有结构体与fops的关联参考1.2.2**

**通过container_of函数来获得私有结构体的指针并操作数据示例**

```c
static long ioctl_hello(struct file *my_file,unsigned int cmd, long unsigned int arg) {
    struct hello *p_hello = container_of(my_file->private_data, struct hello, misc_hello);
    p_hello->a = 1;
    return 0；
}
```

