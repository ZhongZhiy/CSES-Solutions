
注意使用 64位int
```rust

macro_rules! out {
    ($w:expr, $val:expr) => {
        write!($w, "{} ", $val).unwrap();
    };
}

fn solve<R: BufRead, W: Write>(sc: &mut Scanner<R>, wt: &mut W) {
    let mut n: usize = sc.next().unwrap();
    let mut a: Vec<i64> = vec![0; n];
    let mut cnt = 0;
    for i in 0..n {
        a[i] = sc.next().unwrap();
        if i > 0 && a[i] < a[i - 1] {
            cnt += a[i - 1] - a[i];
            a[i] = a[i - 1];
        }
    }
    out!(wt, cnt);
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
