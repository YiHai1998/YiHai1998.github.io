# Rust入门

文档：[Rust 官方文档中文教程 ](https://rustwiki.org/)/ 对应视频：https://www.bilibili.com/video/BV1hp4y1k7SV

官网：https://www.rust-lang.org/zh-CN/

## 开始

下载clion

创建main.rs

```rust
fn main() {
    println!("你好");
}
```

```
cargo run
```

```
glong@glongdeMacBook-Air rust-demo % cargo run
   Compiling rust-demo v0.1.0 (/Users/glong/project/rust-demo)
    Finished dev [unoptimized + debuginfo] target(s) in 0.52s
     Running `target/debug/rust-demo`
你好
```

## cargo

> cargo类似Java的maven，帮助我们管理第三方库

```bash
cargo --version
cargo 1.53.0 (4369396ce 2021-04-27)
```

使用cargo创建项目

```bash
cargo new hello_cargo
```

### cargo.toml

```
[package]
name = "hello_cargo"
version = "0.1.0"
edition = "2018"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
```

其中

```
- name，项目名
- version, 项目版本
- authors， 项目作者
- edition，使用的Rust版本
```

添加依赖，如

```
[dependencies]
rand = "0.3.14"
```

速度非常慢，需要修改cargo镜像源

```
cd /Users/glong/.cargo
```

```
vim config


[source.crates-io]
registry = "https://github.com/rust-lang/crates.io-index"
# 指定镜像
replace-with = 'tuna'

# 清华大学
[source.tuna]
registry = "https://mirrors.tuna.tsinghua.edu.cn/git/crates.io-index.git"
```

### cargo.lock

锁住依赖

### 命令

```bash
cargo build
	cargo build --release	发布使用
cargo check
cargo run

cargo update // 忽略cargo.lock里面的内容，更新依赖
```

## 编写 猜猜看

```rust
use std::io;    // prelude
fn main() {
    println!("猜数字");

    println!("猜测一个数");

    /**
    rust变量默认不可变，所以需要使用mut
    **/

    // let mut foo = 1;
    // let bar = foo;
    // foo = 2;

    let mut guess = String::new();  // ::表示关联函数，类似于java的static静态函数

    io::stdin().read_line(&mut guess).expect("无法读取行");
    // io::Result Ok, Err
    println!("你猜测的数数: {}", guess);
}
```





### 比较大小

```rust
use std::io;    // prelude
use std::cmp::Ordering;
use rand::Rng;  // trait Rng为随机生成数
fn main() {
    println!("猜数字");

    let select_number = rand::thread_rng().gen_range(1, 101);
    println!("神秘数字数 {}", select_number);

    println!("猜测一个数");

    let mut guess = String::new();  // ::表示关联函数，类似于java的static静态函数

    io::stdin().read_line(&mut guess).expect("无法读取行");

    // parse解析，可能是u8，u32等到，所以前面要显示声明变量类型 : u32
    let guess: u32 = guess.trim().parse().expect("Please type a number!");
    // io::Result Ok, Err
    println!("你猜测的数数: {}", guess);

    // match类似于switch，但是它更是一种模式匹配
    match guess.cmp(&select_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("To big"),
        Ordering::Equal => println!("You win"),
    };
}
```

### 多次猜测

上面我们每猜测一次都要重新开启，现在使用循环

```rust
use std::io;    // prelude
use std::cmp::Ordering;
use rand::Rng;  // trait Rng为随机生成数
fn main() {
    println!("猜数字");

    let select_number = rand::thread_rng().gen_range(1, 101);
    println!("神秘数字数 {}", select_number);


    loop {
        println!("猜测一个数");

        let mut guess = String::new();  // ::表示关联函数，类似于java的static静态函数

        io::stdin().read_line(&mut guess).expect("无法读取行");

        // parse解析，可能是u8，u32等到，所以前面要显示声明变量类型 : u32
        let guess: u32 = match guess.trim().parse(){
            Ok(num) => num,
            Err(_) => continue,
        };
        // io::Result Ok, Err
        println!("你猜测的数数: {}", guess);

        // match类似于switch，但是它更是一种模式匹配
        match guess.cmp(&select_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("To big"),
            Ordering::Equal => {
                println!("You win");
                break;
            },
        };
    }
}
```

# Rust常见编程概念

## 变量和可变性

### 变量和常量的区别

常量不能使用`mut`。

#### Rust 常量的命名规范

使用下划线分隔的大写字母单词，并且可以在数字字面值中插入下划线来提升可读性

将遍布于应用程序中的硬编码值声明为常量，易于维护。

```rust
const MAX_POINTS: u32 = 100_000;
```

### 隐藏（Shadowing）

#### 隐藏和mut

`隐藏`与将变量标记为 `mut` 是有区别的。当不小心尝试对变量重新赋值时，如果没有使用 `let` 关键字，就会导致编译时错误。通过使用 `let`，我们可以用这个值进行一些计算，不过计算完之后变量仍然是不变的。

mut不能改变类型

`mut` 与隐藏的另一个区别是，当再次使用 `let` 时，实际上创建了一个新变量，我们可以改变值的类型，但复用这个名字。例如，假设程序请求用户输入空格字符来说明希望在文本之间显示多少个空格，然而我们真正需要的是将输入存储成数字（多少个空格）：

## 数据类型

Rust 是 **静态强类型**（*statically typed*）语言，也就是说在编译时就必须知道所有变量的类型。

### 类型注解

下面不添加类型注解，Rust 编译器会显示错误

```rust
let guess  = "42".parse().expect("Not a number!");
```

```
error[E0282]: type annotations needed
 --> src/main.rs:2:9
  |
3 |     let guess  = "42".parse().expect("Not a number!");
  |         ^^^^^ consider giving `guess` a type
```

```rust
let guess: u32 = "42".parse().expect("Not a number!");
```

### 标量类型

**标量**（*scalar*）类型代表一个单独的值。Rust 有四种基本的标量类型：整型、浮点型、布尔类型和字符类型。



### 复合类型

**复合类型**（*Compound types*）可以将多个值组合成一个类型。Rust 有两个原生的复合类型：元组（tuple）和数组（array）。

#### 元组

```rust
let x: (u32, i128, i32) = (1, 2, 3);
println!("{:#?}", x);
let one = x.2;
println!("{}", one);
```

使用点号（`.`）后跟值的索引来直接访问

#### 数组

## 函数

```rust
fn main() {
    let x = 10;
    let x = plus_one(x);
    println!("{}", x);
}
fn plus_one(x: u32) -> u32{
    x + 1
}
```

