# GLib的使用

## 一、课程介绍

- 介绍
- 为什么需要
- 是什么
- 安装
- 数据结构
- 单元测试
- 日志
- 事件循环

## 二、为什么需要

- 提高效率
  - 节省时间
  - 减少bug

- 避免重复造轮子

## 三、是什么

​	GLib是一个综合用途的实用的轻量级的C程序库，它提供C语言的常用的数据结构的定义、相关的处理函数，有趣而实用的宏，可移植的封装和一些运行时机能，如事件循环、线程、动态调用、对象系统等的API。它能够在类UNIX的操作系统平台（如LINUX， HP-UNIX等），WINDOWS，OS2和BeOS等操作系统台上运行。

​	GLib由基础类型、对核心应用的支持、实用功能、数据类型和对象系统五个部分组成的。

## 四、安装

```shell
sudo apt install libglib2.0-dev
//sudo apt install build-essential pkg-config
```

### 1、编译环境配置

#### 1.1、task.json添加参数

```
                "-I/usr/include/glib-2.0",  
                "-I/usr/lib/x86_64-linux-gnu/glib-2.0/include",  
                "-lglib-2.0",  
```

#### 1.2、c_cpp_properties.json添加参数

```
                "/usr/include/glib-2.0",
                "/usr/lib/x86_64-linux-gnu/glib-2.0/include"
```

#### 1.3、注意

​	唯一需要注意的是GLib2是LGPL协议开源的库,所以你应该动态链接它,而不是把它静态编译到你的程序中,从而避免你的代码也要使用LGPL协议进行开源.

## 五、数据结构

#### 1.1、**GList** 和 **GSList**

双向链表和单向链表，用于存储元素的集合。这些链表允许在任意位置插入和删除元素。

#### 1.2、**GQueue**

基于双向链表的队列实现，支持入队（enqueue）和出队（dequeue）操作。

#### 1.3、**GArray** 和 **GPtrArray**

动态数组，用于存储固定大小元素或指针的集合。它们可以自动调整大小以适应更多的元素。

#### 1.4、**GByteArray**

用于存储字节数据的动态数组。

#### 1.5、**GHashTable**

 哈希表，允许根据键快速查找和存储值。它支持多种键和值的数据类型。

#### 1.6、**GTree**

 二叉树数据结构，用于存储排序的元素。

#### 1.7、**GString**

 可变字符串，用于动态构建和修改字符串。

#### 1.8、**GStringChunk**

 字符串块，用于高效地管理和分配字符串。

## 六、单元测试

#### 1、为什么需要

​	将单元测试想象为**质检员**，将要检验的函数想象为**待出厂的零件**。

#### 2、是什么

GLib 测试框架主要包含三个概念：

- 测试案例（Test Case）：由测试函数与夹具（Fixture）构成。
- 夹具（估计是从机械领域引入的概念，被测的函数被视为零件）：为测试函数准备数据，并负责测试环境的构建与销毁。
- 测试集（Test Suit）：由测试案例构成的集合。

#### 3、怎么使用

```c
#include<stdio.h>
#include<glib.h>

typedef struct A
{
    int v;
} A;

void test()
{
    g_assert_true (2==3);
    g_print("test\n");
}

void foo(gconstpointer test_data)
{
    A* a = (A*)test_data;
    g_print("A.v = %d\n", a->v);
}

/* run a test with fixture setup and teardown */
typedef struct
{
    guint seed;
    guint prime;
    gchar *msg;
} Fixturetest;

static void fixturetest_setup(Fixturetest *fix, gconstpointer test_data)
{
    g_assert_true(test_data == (void *)0xc0cac01a);
    fix->seed = 18;
    fix->prime = 19;
    fix->msg = g_strdup_printf("%d", fix->prime);
}

static void fixturetest_test(Fixturetest *fix, gconstpointer test_data)
{
    guint prime = g_spaced_primes_closest(fix->seed);
    g_assert_cmpint(prime, ==, fix->prime);
    prime = g_ascii_strtoull(fix->msg, NULL, 0);
    g_assert_cmpint(prime, ==, fix->prime);
    g_assert_true(test_data == (void *)0xc0cac01a);
}

static void fixturetest_teardown(Fixturetest *fix, gconstpointer test_data)
{
    g_assert_true(test_data == (void *)0xc0cac01a);
    g_free(fix->msg);
}

int main(int argc, char **argv)
{
    gchar *base_name = g_path_get_basename(argv[0]);
    g_set_prgname(base_name);
    g_free(base_name);

    g_debug("start test...");

    A a;
    a.v = 100;

    g_test_init(&argc, &argv, NULL);
    g_test_add_func("/t", test);
    g_test_add_data_func("/td", &a, foo);

    g_test_add("/t1", Fixturetest, (void*)0xc0cac01a, fixturetest_setup, fixturetest_test, fixturetest_teardown);
    int ret = g_test_run();
    g_message("A:%d", a.v);
    return ret;
}
```

## 七、日志

在 GLib 中，日志处理通常是通过 `g_log()` 函数和相关的宏来实现的。`g_log()` 函数允许你记录不同级别的日志消息，并且你可以通过设置日志处理器（log handler）来定制日志消息的输出。

以下是一些使用 GLib 处理日志的常用函数和代码片段：

首先，你需要包含必要的头文件，并设置日志级别和处理器：

```c
#include <glib.h>  
  
// 日志级别定义  
#define MY_LOG_DOMAIN "my-app"  
  
// 自定义日志处理器函数  
static void my_log_handler(const gchar *log_domain, GLogLevelFlags log_level, const gchar *message, gpointer user_data) {  
    // 输出到控制台
    //g_print("%s: %s\n", log_domain, message);  
    // 打开日志文件，追加方式写入
    FILE *log_file = fopen(LOG_FILE_PATH, "a");
    if (log_file == NULL) {
        perror("fopen");
        return;
    }

    // 将日志信息写入到文件
    fprintf(log_file, "%s: %s\n", log_domain, message);

    // 关闭日志文件
    fclose(log_file);
}  
  
int main(int argc, char *argv[]) {  
    // 初始化 GLib  
    g_type_init();  
  
    // 添加自定义的日志处理器  
    g_log_set_handler(MY_LOG_DOMAIN, G_LOG_LEVEL_MASK, my_log_handler, NULL);  
  
    // 设置日志的默认输出函数（可选）  
    // g_log_set_default_handler(my_log_handler, NULL);  
  
    // 记录不同级别的日志消息  
    g_log(MY_LOG_DOMAIN, G_LOG_LEVEL_INFO, "This is an info message");  
    g_log(MY_LOG_DOMAIN, G_LOG_LEVEL_WARNING, "This is a warning message");  
    g_log(MY_LOG_DOMAIN, G_LOG_LEVEL_ERROR, "This is an error message");  
  
    // 也可以使用宏来简化日志记录  
    g_message("This is a message with g_message()");  
    g_warning("This is a warning with g_warning()");  
    g_error("This is an error with g_error()"); // 这通常会终止程序，因为它调用 g_assert_not_reached()  
  
    // 注意：g_error() 在非调试模式下可能会导致程序终止，因为它内部调用了 g_assert_not_reached()。  
    // 在生产代码中，你可能想要避免使用 g_error()，或者确保它不会导致程序意外终止。  
  
    return 0;  
}
```

在上面的代码中，我们首先定义了一个日志域 `MY_LOG_DOMAIN`，它通常用于标识你的应用程序或库。然后，我们定义了一个自定义的日志处理器函数 `my_log_handler()`，它简单地将日志消息打印到控制台。我们使用 `g_log_set_handler()` 将这个处理器添加到我们的日志域中，并设置它处理所有级别的日志消息（`G_LOG_LEVEL_MASK`）。

我们还展示了如何使用 `g_log()` 函数直接记录不同级别的日志消息，以及使用 `g_message()`, `g_warning()`, 和 `g_error()` 宏来简化日志记录。这些宏内部实际上调用了 `g_log()`，并自动添加了日志级别和当前的文件名及行号。

请注意，`g_error()` 宏在调用时会触发一个断言失败，这通常会导致程序终止。在生产代码中，你可能想要避免使用 `g_error()`，或者确保它不会导致程序意外终止，尤其是在你不希望程序因为日志消息而退出的情况下。

此外，你还可以使用 `g_log_set_default_handler()` 来设置默认的日志处理器，它将应用于没有特定处理器的所有日志域。这在你想要为所有日志消息应用统一的处理逻辑时很有用。

## 八、事件循环

​	`glib` 的事件循环是一个强大的机制，它允许应用程序以非阻塞的方式等待和响应多种类型的事件，包括IO事件（如套接字读写）、定时器事件以及信号等。事件循环不断地检查是否有待处理的事件，并在有事件发生时调用相应的回调函数。

​	`	glib` 的事件循环是通过 `GMainLoop` 对象来管理的。你可以创建一个 `GMainLoop` 对象，然后启动它，让它开始监视事件。当某个事件发生时，`glib` 会调用与该事件关联的回调函数。如果你想退出事件循环，可以在回调函数中调用 `g_main_loop_quit()` 函数。

```c
#include <glib.h>  
#include <stdio.h>  
  
// 定时器回调函数  
gboolean timer_callback(gpointer user_data) {  
    g_print("Timer fired!\n");  
    // 返回TRUE表示定时器应该继续运行  
    return TRUE;  
}  
  
int main(int argc, char *argv[]) {  
    g_type_init(); // 初始化glib类型系统  
  
    // 创建GMainLoop实例  
    GMainLoop *loop = g_main_loop_new(NULL, FALSE);  
  
    // 创建一个定时器，每隔1000毫秒触发一次  
    guint timer_id = g_timeout_add_seconds(1, timer_callback, NULL);  
  
    // 开始事件循环  
    g_print("Starting event loop...\n");  
    g_main_loop_run(loop);  
  
    // 移除定时器（虽然在这个例子中，由于main loop已经结束，这一步其实是不必要的）  
    g_source_remove(timer_id);  
  
    // 释放GMainLoop实例  
    g_main_loop_unref(loop);  
  
    return 0;  
}
```

​	在这个示例中，我们创建了一个 `GMainLoop` 对象，并添加了一个定时器事件和一个IO事件。定时器事件每2秒触发一次，并调用 `timeout_callback` 函数。IO事件监视 `/dev/null` 文件的输入（这只是一个示例，实际应用中你应该监视真实的IO通道），并在有输入时调用 `io_callback` 函数。最后，我们调用 `g_main_loop_run()` 启动事件循环。

注意，在实际应用中，你通常不会直接创建 `GIOChannel` 对象来监视IO事件。相反，你会使用 `gio` 提供的更高级别的抽象，如 `GSocket` 或 `GInputStream`/`GOutputStream`，它们会自动为你处理底层的IO通道和事件。

这个示例是为了演示 `glib` 事件循环的基本工作原理。在实际的项目中，你可能需要根据具体需求来添加和配置不同类型的事件。

九、实战应用

