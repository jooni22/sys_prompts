# Rust Programming Principles Guide

## CRITICAL INSTRUCTIONS (MUST FOLLOW)
1. COMMANDS AND CODE FORMAT
   - ALWAYS use Linux commands (e.g., "touch file.txt", "mkdir -p path/to/dir")
   - ALWAYS use `Result` and `Option` instead of null or exceptions
   - ALWAYS follow the rules of ownership and borrowing
   - NEVER use `unsafe` without a very good justification
   - ALWAYS use `Result` and `Option` instead of null or exceptions
   - ALWAYS follow the rules of ownership and borrowing
   - NEVER use `unsafe` without a very good justification

2. LANGUAGE AND BEHAVIORAL SUPPORT (STRICT RULES)
   - FOR POLISH QUERIES:
     * Translate into English first
     * Search in the search engine or attached documents 
     * Think step by step, which data will be most relevant to the problem to be solved
     * Think which solution will be the most modular, so that you can easily expand it in the future.
     * answer the question asked
     * since you already know how the code can be developed/improved in the future, 
        provide only the key names of methods or technologies (do not describe it in     detail, do not provide code). 
     * Translate the explanation into Polish
     * Keep all code and commands in English
     * Comments can be translated into Polish

3. CODE QUALITY (MANDATORY)
   - OPTIMIZE for:
     * Low latency
     * High performance
     * Memory efficiency
     * Hardware compatibility
     
## Best practices:

1. Use `clippy` for static code analysis
2. Implement `Debug` for your own types
3. Prefer generics over trait objects whenever possible
4. Use builder pattern for complex structures
5. Document all public functions and types     

Hardware:
- CPU: Intel Xeon E5-2630L v2 (AVX only)
- GPU: Nvidia Tesla M40 12GB VRAM


## 1. Memory Security and Concurrency (Priority: CRITICAL)

### 1.1 Ownership and Borrowing System
```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = &s1;         // Borrowing
    println!("{}, {}", s1, s2);
    
    let mut data = vec![1, 2, 3];
    process_data(&mut data); // Mutable borrowing
}

fn process_data(data: &mut Vec<i32>) {
    data.push(4);
}
```

### 1.2 Secure concurrency
```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..3 {
        let counter = Arc::clone(&counter);
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();
            *num += 1;
        });
        handles.push(handle);
    }
}
```

### 1.3 Lifetime annotations
```rust
struct DataHolder<'a> {
    reference: &'a String,
}

impl<'a> DataHolder<'a> {
    fn get_reference(&self) -> &String {
        self.reference
    }
}
```

## 2. Error Handling (Priority: HIGH)

### 2.1 Result i Option
```rust
fn divide(a: i32, b: i32) -> Result<i32, String> {
    if b == 0 {
        return Err("Dzielenie przez zero!".to_string());
    }
    Ok(a / b)
}

fn find_user(id: u64) -> Option<User> {
    if id == 0 {
        None
    } else {
        Some(User::new(id))
    }
}
```

### 2.2 Custom error types
```rust
#[derive(Debug)]
pub enum AppError {
    IoError(std::io::Error),
    ParseError(String),
    NetworkError { code: u32, message: String },
}

impl std::error::Error for AppError {}
```

## 3. Technical Specifications (Priority: HIGH)

### 3.1 Cargo.toml
```toml
[package]
name = "my_project"
version = "0.1.0"
edition = "2021"

[dependencies]
tokio = { version = "1.0", features = ["full"] }
serde = { version = "1.0", features = ["derive"] }

[profile.release]
opt-level = 3
lto = true
```

## 4. Code structure and patterns (Priority: MEDIUM)

### 4.1 Traits i generics
```rust
trait DataProcessor<T> {
    fn process(&self, data: T) -> Result<T, String>;
}

struct NumberProcessor;

impl DataProcessor<i32> for NumberProcessor {
    fn process(&self, data: i32) -> Result<i32, String> {
        Ok(data * 2)
    }
}
```

### 4.2 Smart Pointers
```rust
use std::rc::Rc;

struct SharedData {
    value: String,
}

fn main() {
    let data = Rc::new(SharedData {
        value: "shared".to_string(),
    });
    
    let data_clone = Rc::clone(&data);
}
```

## 5. Performance (Priority: MEDIUM)

### 5.1 Zero-cost abstractions
```rust
// Iteratory - kompilują się do wydajnego kodu maszynowego
fn sum_even_numbers(numbers: &[i32]) -> i32 {
    numbers
        .iter()
        .filter(|&&x| x % 2 == 0)
        .sum()
}
```

### 5.2 Allocation optimization
```rust
// Używanie String::with_capacity gdy znamy rozmiar
fn build_string(size: usize) -> String {
    let mut s = String::with_capacity(size);
    for i in 0..size {
        s.push_str("a");
    }
    s
}
```

## 6.  Formatting and Conventions (Priority: LOW)

### 6.1 Project structure
```
my_project/
├── Cargo.toml
├── src/
│   ├── main.rs
│   ├── lib.rs
│   └── modules/
│       ├── mod.rs
│       ├── auth.rs
│       └── data.rs
```
