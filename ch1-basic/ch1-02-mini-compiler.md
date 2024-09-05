# 1.2. 最小编译器

我们先从最小的整数开始，每个整数表示一个返回该值状态码的程序。

比如 0 表示 `os.Exit(0)`。它对应以下的Go程序：

```wa
import "os"

func main {
	os.Exit(0)
}
```

对应以下的LLVM-IR代码:

```ll
define i32 @main() {
	ret i32 0
}
```

入口是`@main`函数，`ret`指令返回i32类型的0。

可以通过以下命令编译并执行这个汇编程序：

```
$ clang -o a.out _main.ll
$ ./a.out
$ echo $?
0
```

clang 将汇编程序编译为本地可执行程序，然后执行 a.out 程序，最后通过shell的 `echo $?` 命令查看 a.out 的退出状态码。

最小编译器就是将输入的整数翻译为可执行程序的返回该状态码的本地程序：

```wa
func compile(code: string) {
	output := strings.ReplaceAll(tmpl, `{{.V}}`, code)
	println(output)
}

const tmpl = `
define i32 @main() {
	ret i32 {{.V}}
}
`
```

其中 compile 是编译函数，将从stdin输入的代码先编译为汇编程序，然后调用clang将汇编程序编译为本地可执行程序（`tmpl`是输出汇编的模板）。

比如有以下main函数表示将123编译为可执行程序：

```wa
func main {
	compile(`123`)
}
```

通过以下命令将输入的状态码编译为一个对应的可执行程序：

```shell
$ wa run main.wa > _main.ll
$ clang -Wno-override-module -o a.out _main.ll
$ ./a.out
$ echo $?
123
```

这样我们就实现了一个只能编译整数到本地可执行程序的最小编译器。
