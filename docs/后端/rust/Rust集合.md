# Rust集合

## Vector

```rust
fn main() {
    // let v: Vec<i32> = Vec::new();	// 必须注解创建的类型，因为创建的时候没有元素，无法推断
    lev v = vec![1,2,3];	// 宏
}
```

上面提到没有类型注解会报错，但是Rust的有强大的上下文推断

```rust
fn main() {
    let mut v = Vec::new();
    v.push(1);
}
```

### 删除Vecotr

### 读取Vectort

索引和get处理访问越界

```rust
let v = vec![1, 2, 3, 4, 5];

let third: &i32 = &v[2];
println!("The third element is {}", third);

match v.get(2) {
    Some(third) => println!("The third element is {}", third),
    None => println!("There is no third element."),
}
```

使用 `&` 和 `[]` 返回一个引用；或者使用 `get` 方法以索引作为参数来返回一个 `Option<&T>`。

### 遍历 vector 中的元素

```rust
fn main() {
let v = vec![100, 32, 57];
for i in &v {
    println!("{}", i);
}
}
```



可变遍历修改

```rust

#![allow(unused_variables)]
fn main() {
let mut v = vec![100, 32, 57];
for i in &mut v {
    *i += 50;	// * 表示解引用
}
}
```

## String

### 更新

push_str：字符串

push：字符





### 拼接

format!宏

```rust

fn main() {
    let s1 = "hello".to_string();
    let s2 = String::from("world");
    let s = format!("{},{}", s1, s2);

    println!("{}", s);
}
```

+，这种方法略显笨重。

```rust
let s1 = String::from("tic");
let s2 = String::from("tac");
let s3 = String::from("toe");

let s = s1 + "-" + &s2 + "-" + &s3;
println!("{}", s);
```

