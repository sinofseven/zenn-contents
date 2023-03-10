---
title: "RustからVimを呼び出して使用する"
---

CLI Toolを作るにあたって、`git commit`のようにviを呼び出せるようにしたかった。

以下の内容を参考に実装してみた。
- [Running commands in rust (vim) - Stack Overflow](https://stackoverflow.com/questions/60255889/running-commands-in-rust-vim)


```rust:src/main.rs
use std::fs;

fn main() -> Result<(), String> {
    let base_text = "1\n22\n333\n444\n";
    let file_name = "text.txt";

    fs::write(file_name, base_text).map_err(|e| format!("failed to write: {}", e))?;

    let mut a = std::process::Command::new("vi")
        .arg(file_name)
        .spawn()
        .map_err(|e| format!("failed to spawn: {}", e))?;

    let b = a.wait().map_err(|e| format!("failed to wait: {}", e))?;

    println!("exit code: {:?}", b.code());

    let c = fs::read_to_string(file_name).map_err(|e| format!("failed to read: {}", e))?;

    println!("is changed: {}", base_text != &c);

    Ok(())
}
```