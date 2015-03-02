rust-systemd
============

Only has bindings for the journal and daemon APIs at the moment.

journal
-------
Journal sending is supported, and systemd::journal::Journal is a (low
functionality) wrapper around the read API.

HOWTO log to journald:

```rust
# cat >Cargo.toml <<EOF
[dependencies.systemd]
git = "https://github.com/jmesmon/rust-systemd"
EOF

# editor src/main.rs


#![feature(phase)]
#[phase(plugin,link)] extern crate log;
#[phase(plugin,link)] extern crate systemd;
use systemd::journal;

fn main() {
   use systemd::journal;
   use log::set_logger;
   journal::print(1, format!("Rust can talk to the journal: {}",
                             4i).as_slice());
   journal::send(["CODE_FILE=HI", "CODE_LINE=1213", "CODE_FUNCTION=LIES"]);
   set_logger(box journal::JournalLogger);
   log!(0, "HI");
   sd_journal_log!(4, "HI {}", 2i);
}
```

daemon
------
The daemon API mostly offers tools for working with raw filehandles passed to
the process by systemd on socket activation. Since raw filehandles are not well
supported in Rust, it's likely these functions will mostly be helpful in
managing program flow; actual socket code will have to use the libc crate.

TODO
----

 - [ ] rustdoc
 - [ ] other systemd apis
 - [ ] pass travis automated tests
