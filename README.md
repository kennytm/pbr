# Terminal progress bar for Rust

> This is a fork of the original [`pbr`](https://crates.io/crates/pbr) package, which included the
> [PR #120](https://github.com/a8m/pb/pull/120). Without this PR the bar will just deadlock the CLI.
> Even though the PR has merged into the master branch, the original author did not publish it to
> crates.io, forcing its user to use a `git` dependency before this fork existed.
>
> Once the original `pbr` was published with the above PR included, this package will be yanked.

[![Latest version](https://img.shields.io/crates/v/pbr-120.svg)](https://crates.io/crates/pbr-120)
[![License](https://img.shields.io/crates/l/pbr.svg)](https://github.com/a8m/pb/blob/master/LICENSE.md)
[![Docs](https://img.shields.io/badge/docs-reference-blue.svg)](https://a8m.github.io/pb/doc/pbr/index.html)
[![Build Status](https://travis-ci.org/a8m/pb.svg?branch=master)](https://travis-ci.org/a8m/pb)
[![Gitter](https://badges.gitter.im/a8m/pb.svg)](https://gitter.im/a8m/pb?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

Console progress bar for Rust Inspired from [pb](http://github.com/cheggaaa/pb), support and
tested on MacOS, Linux and Windows

![Screenshot](https://github.com/a8m/pb/blob/master/gif/rec_v3.gif)

[Documentation](https://a8m.github.io/pb/doc/pbr/index.html)

### Examples
1. simple example

```rust
use pbr::ProgressBar;
use std::thread;

fn main() {
    let count = 1000;
    let mut pb = ProgressBar::new(count);
    pb.format("╢▌▌░╟");
    for _ in 0..count {
        pb.inc();
        thread::sleep_ms(200);
    }
    pb.finish_print("done");
}
```

2. MultiBar example. see full example [here](https://github.com/a8m/pb/blob/master/examples/multi.rs)
```rust
use std::thread;
use pbr::MultiBar;
use std::time::Duration;

fn main() {
    let mut mb = MultiBar::new();
    let count = 100;
    mb.println("Application header:");

    let mut p1 = mb.create_bar(count);
    let _ = thread::spawn(move || {
        for _ in 0..count {
            p1.inc();
            thread::sleep(Duration::from_millis(100));
        }
        // notify the multibar that this bar finished.
        p1.finish();
    });

    mb.println("add a separator between the two bars");

    let mut p2 = mb.create_bar(count * 2);
    let _ = thread::spawn(move || {
        for _ in 0..count * 2 {
            p2.inc();
            thread::sleep(Duration::from_millis(100));
        }
        // notify the multibar that this bar finished.
        p2.finish();
    });

    // start listen to all bars changes.
    // this is a blocking operation, until all bars will finish.
    // to ignore blocking, you can run it in a different thread.
    mb.listen();
}
```

3. Broadcast writing (simple file copying)

```rust
#![feature(io)]
use std::io::copy;
use std::io::prelude::*;
use std::fs::File;
use pbr::{ProgressBar, Units};

fn main() {
    let mut file = File::open("/usr/share/dict/words").unwrap();
    let n_bytes = file.metadata().unwrap().len() as usize;
    let mut pb = ProgressBar::new(n_bytes);
    pb.set_units(Units::Bytes);
    let mut handle = File::create("copy-words").unwrap().broadcast(&mut pb);
    copy(&mut file, &mut handle).unwrap();
    pb.finish_print("done");
}
```

### License
MIT

