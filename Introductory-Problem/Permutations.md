n = 2, 4 无解

n = 4, 时 `2, 4, 1, 3` 满足条件, 也就是, 当`n >=4` 先输出 偶数, 再输出计数就可以满足条件

```rust
macro_rules! out {
    ($w:expr, $val:expr) => {
        write!($w, "{} ", $val).unwrap();
    };
}

fn solve<R: BufRead, W: Write>(sc: &mut Scanner<R>, wt: &mut W) {
    let n: usize = sc.next().unwrap();
    if n == 2 || n == 3 {
        out!(wt, "NO SOLUTION");
        return;
    }

    for i in 1..=n {
        if i % 2 == 0 {
            out!(wt, i);
        }
    }
    for i in 1..=n {
        if i % 2 != 0 {
            out!(wt, i);
        }
    }
}

fn main() {
    let stdin = io::stdin();
    let stdout = io::stdout();

    let mut scan = Scanner::new(stdin.lock());
    let mut write = io::BufWriter::new(stdout.lock());
    let mut t: i32 = 1;
    // t = scan.next().unwrap();
    while t > 0 {
        t -= 1;
        solve(&mut scan, &mut write);
    }
    write.flush().unwrap();
}

use std::io::{self, BufRead, Write};

struct Scanner<R> {
    reader: R,
    buf: Vec<String>,
}

impl<R: BufRead> Scanner<R> {
    fn new(reader: R) -> Self {
        Self {
            reader,
            buf: Vec::new(),
        }
    }

    fn next<T: std::str::FromStr>(&mut self) -> Option<T> {
        loop {
            if let Some(token) = self.buf.pop() {
                return token.parse().ok();
            }
            let mut line = String::new();
            match self.reader.read_line(&mut line) {
                Ok(0) => return None,
                Ok(_) => {
                    self.buf = line.split_whitespace().map(String::from).rev().collect();
                }
                Err(_) => return None,
            }
        }
    }
}

```
