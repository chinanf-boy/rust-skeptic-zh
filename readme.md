# budziq/rust-skeptic [![explain]][source] [![translate-svg]][translate-list]

<!-- [![size-img]][size] -->

[explain]: http://llever.com/explain.svg
[source]: https://github.com/chinanf-boy/Source-Explain
[translate-svg]: http://llever.com/translate.svg
[translate-list]: https://github.com/chinanf-boy/chinese-translate-list
[size-img]: https://packagephobia.now.sh/badge?p=Name
[size]: https://packagephobia.now.sh/result?p=Name

「 作一个 Rust 文档 怀疑论者（Skeptic） 」

[中文](./readme.md) | [english](https://github.com/budziq/rust-skeptic)

---

## 校对 ✅

<!-- doc-templite START generated -->
<!-- repo = 'budziq/rust-skeptic' -->
<!-- commit = 'cf98a546a023b389c11068b218b28d025014271e' -->
<!-- time = '2019-03-14' -->

| 翻译的原文 | 与日期        | 最新更新 | 更多                       |
| ---------- | ------------- | -------- | -------------------------- |
| [commit]   | ⏰ 2019-03-14 | ![last]  | [中文翻译][translate-list] |

[last]: https://img.shields.io/github/last-commit/budziq/rust-skeptic.svg
[commit]: https://github.com/budziq/rust-skeptic/tree/cf98a546a023b389c11068b218b28d025014271e

<!-- doc-templite END generated -->

- [x] readme

### 贡献

欢迎 👏 勘误/校对/更新贡献 😊 [具体贡献请看](https://github.com/chinanf-boy/chinese-translate-list#贡献)

## 生活

[hIf help, **buy** me coffee —— 营养跟不上了，给我来瓶营养快线吧! 💰](https://github.com/chinanf-boy/live-need-money)

---

# 作一个 Rust 文档 Skeptic（Skeptic）

[![Unix build status](https://travis-ci.org/budziq/rust-skeptic.svg?branch=master)](https://travis-ci.org/budziq/rust-skeptic)
[![Windows build status](https://ci.appveyor.com/api/projects/status/l1f74hon37wt2vce/branch/master?svg=true)](https://ci.appveyor.com/project/budziq/rust-skeptic/branch/master)
[![crates.io](https://img.shields.io/crates/v/skeptic.svg)](https://crates.io/crates/skeptic)
[![Documentation](https://docs.rs/skeptic/badge.svg)](https://docs.rs/skeptic)

通过 Cargo 测试您的 Rust Markdown。

## 入门

把下面内容放进`Cargo.toml`，这样就添加了`skeptic`依赖：

```toml
[build-dependencies]
skeptic = "0.13"

[dev-dependencies]
skeptic = "0.13"
```

也在`Cargo.toml`的`[package]`部分，添加：

```toml
build = "build.rs"
```

这会增加了一个[build(构建) 脚本](https://doc.crates.io/build-script.html)。通过它，您能告诉 **Skeptic** ，是从(哪)一组 Markdown 文件开始，构建测试用例。

在`build.rs`写下(这个)，来测试`README.md`的所有 Rust 代码块：

```rust,no_run
extern crate skeptic;

fn main() {
    // generates doc tests for `README.md`.
    skeptic::generate_doc_tests(&["README.md"]);
}
```

如果要测试多个 markdown 文件，只需构建一个文件名列表，并将其提供给`generate_doc_tests`就好。为了帮助你，方法`markdown_files_of_directory`能创建一个这样的列表，在指定目录中，枚举每个 markdown 文件。您可以根据需要，在此列表中添加更多文件：

```rust,no_run
extern crate skeptic;

use skeptic::*;

fn main() {
    // Add all markdown files in directory "book/".
    let mut mdbook_files = markdown_files_of_directory("book/");
    // Also add "README.md" to the list of files.
    mdbook_files.push("README.md".into());
    generate_doc_tests(&mdbook_files);
}
```

最后，在`tests/skeptic.rs`中，将下面的宏，绑定到生成的测试用例，(最终)交给`cargo test`：

```rust,ignore
include!(concat!(env!("OUT_DIR"), "/skeptic-tests.rs"));
```

现在`README.md`的任何 Rust 代码块，将在`cargo test`期间进行测试。

## 用户指南

Rust `Skeptic` 不是基于 rustdoc。虽然在许多情况下表现相似，但并非完全一致。这是 Skeptic 系统的最低点。

_注意：[本`README.md`文件，就是被 Rust Skeptic 所测试的](https://github.com/budziq/rust-skeptic/blob/master/build.rs)。因为能够说明，(具体是)如何使用 markdown 语法的，所以，本 README 本身的(代码块)标记很时髦，下面的输出也是如此，特别是在说明 Markdown 的代码栅栏时（<code>\```rust</code>)。_

_你必须要求代码块明确表明`rust`，(才能)进行 Rust 测试_，也就是<code>```rust</code>。这与 rustdoc 不同，后者假设代码块是 Rust。这样做的原因，是常见的 Markdown 解析器，就像 GitHub 上使用的，不会默认假设为 Rust：所以你只能，要么 Rust 语法高亮和测试都有，要么都没有。

所以，以下内容是不会由 Skeptic 测试的。

````
```
let this_is_not_going_to_be_compiled_and_run = @all;
It doesn't really matter what's in here.
```
````

为了表示 Rust 代码，标记上代码块`rust`：

````rust,ignore
```rust
fn main() {
   println!("Calm your skepticism. This example is verified.");
}
```
````

Skeptic 会去理解代码块'info string（信息字符串）'上的其他单词（应该用逗号`,`分隔，这与 GitHub 兼容）。这些词会改变了测试的解释方式：`ignore`，`no_run`，和`should_panic`。

### `ignore`信息字符串

`ignore`info 字符串会导致，(代码)完全忽略测试。它不会在测试期间编译或运行。如果一个示例是用 Rust 编写的（并且您希望它能高亮），但是已知它是不完整的（因无法编译），这能用到。

````rust,ignore
```rust,ignore
fn do_amazing_thing() -> i32 {
   // TODO: How do I do this?
   unimplemented! whatever I'm distracted, oh cookies!
```
````

### `no_run`信息字符串

`no_run`info 字符串会导致，示例代码在测试期间不运行。代码标记上`no_run`，但是仍然会被编译。这对那些可能有副作用或依赖的，也不指望测试通过的示例或者测试代码来说，会有用。

````rust,ignore
```rust,no_run
fn do_amazing_thing() -> i32 {
   // TODO: How do I do this?
   unimplemented!()
}

fn main() {
   do_amazing_thing();
}
```
````

### `should_panic`信息字符串

`should_panic`给出了(代码)唯一通过的方式，就是需要一个`panic!()`所导致的终止。

````rust,ignore
```rust,should_panic
fn main() {
   assert!(1 == 100);
}
```
````

## Skeptic 模板

与 rustdoc 不同，_默认情况下，Skeptic 不会在测试前，修改示例_。Skeptic 的例子会放在'.rs'文件中，编译然后运行。

这意味着 -_默认情况下_- Skeptic 的例子需要一个`main`函数，如上面的所有例子。隐含包装了`main`，以及注射自定义的`extern crate`语句，还有 crate(箱子) 的属性由模板控制。

文档(document:markdown)的模板位于单独的文件中，该文件位于(文件系统上的)文档旁边，并且与文档文件具有相同的全名，但具有附加的 `.skt.md` 模版后缀。

例如，这个文件，`README.md`，将模板存储在`README.md.skt.md`。

此方案允许 Markdown 渲染器自然显示 markdown，而需要不显示模板本身。虽然奇怪，但文件扩展名是一致，因此模板本身被解释为有效的 markdown。

考虑这个例子：

```rust,skt-foo
let p = PathBuf::from("foo");
println!("{:?}", p);
```

如果没有定义`main`和导入`PathBuf`，此示例将无法编译（也就是无法通过 Skeptic 的测试），但示例本身不包含该样板。替代该示例的，是注释上的`skt-foo`，这正是*Skeptic 模板 foo*，上面的代码像这样写：

````rust,ignore
```rust,skt-foo
let p = PathBuf::from("foo");
println!("{:?}", p);
```
````

这告诉 Skeptic 在模板文件中，查找具有相同标记的另一个 markdown 块`skt-foo`注释，并使用标准 Rust `format!`宏将它们组合在一起。这是模板的样子：

````rust,ignore
```rust,skt-foo
use std::path::PathBuf;

fn main() {{
    {}
}}
```
````

模板是[Rust format 标准](https://doc.rust-lang.org/std/fmt/index.html)，必须采用一个参数（即它们需要包含字符串“{}”）。这还有些[(旧) template 示例](https://github.com/budziq/rust-skeptic/blob/master/template-example.md)的更多信息。

请注意，在模板中，真正的大括号需要加倍。

## 旧式的文档全局模板

在一份文件中，一个`rust`代码块，若是标记上`skeptic-template`，就会用来作为文档中，所有未明确标记示例的模板。

````rust,ignore
```rust,skeptic-template
use std::path::PathBuf;

fn main() {{
    {}
}}
```
````

## Rustdoc 风格的未显示线条`#`

像 rustdoc 一样，Skeptic 在编译代码之前，会删除任何代码行的前缀的`#`。在显示期间，隐藏这些线条需要在 markdown 渲染器中，进行自定义支持。

## 执照

根据任何一种许可

- Apache 许可证，2.0 版（[LICENSE-APACHE](LICENSE-APACHE)要么<https://apache.org/licenses/LICENSE-2.0>）
- MIT 许可证（[LICENSE-MIT](LICENSE-MIT)要么<https://opensource.org/licenses/MIT>）

### 贡献

除非您另有明确说明，否则您在 Apache-2.0 许可中定义的任何有意提交以包含在您工作中的贡献应按上述方式进行双重许可，不附加任何其他条款或条件。
