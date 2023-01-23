## Keyring-rs
[![CI](https://github.com/hwchen/keyring-rs/actions/workflows/build.yaml/badge.svg)](https://github.com/hwchen/keyring-rs/actions?query=workflow%3Abuild)
[![Crates.io](https://img.shields.io/crates/v/keyring.svg?style=flat-square)](https://crates.io/crates/keyring)
[![API Documentation on docs.rs](https://docs.rs/keyring/badge.svg)](https://docs.rs/keyring)

A cross-platform library to manage storage and retrieval of passwords (and other credential-like secrets) in the underlying platform secure store, with a fully-developed example that provides a command-line interface.

Published on [crates.io](https://crates.io/crates/keyring)

## Usage

__Currently supports Linux, iOS, macOS, and Windows.__ Please file issues if you have any problems or bugs!

To use this library in your project add the following to your `Cargo.toml` file:

```toml
[dependencies]
keyring = "1"
```

This will give you access to the `keyring` crate in your code. Now you can use  the `Entry::new` function to create a new keyring entry. The `new` function expects a non-empty `service` name and a non-empty `username` which together identify the entry.

Passwords can be added to an entry using its `set_password` method.  They can then be read back using the `get_password` method, and deleted using the `delete_password` method.  (The persistence of the `Entry` is determined via Rust rules, so deleting the password doesn't delete the entry, but it does delete the underlying platform credential which was used to store the password.)

```rust
extern crate keyring;

use std::error::Error;

fn main() -> Result<(), Box<dyn Error>> {
    let service = "my_application";
    let username = "my_name";
    let entry = keyring::Entry::new(&service, &username);

    let password = "topS3cr3tP4$$w0rd";
    entry.set_password(&password)?;

    let password = entry.get_password()?;
    println!("My password is '{}'", password);

    entry.delete_password()?;
    println!("My password has been deleted");

    Ok(())
}
```

## Errors

Creating and operating on entries can yield a `keyring::Error` which provides both a platform-independent code that classifies the error and, where available, underlying platform errors and/or more information about what went wrong.

## Examples

The keychain-rs project contains a sample application (`cli`) and a sample library (`ios`).

The application is a command-line interface to the keyring.  This can be a great way to explore how the library is used, and it allows experimentation with the use of different service names, usernames, and targets.  When run in "singly verbose" mode (-v), it outputs the retrieved credentials on each `get` run.  When run in "doubly verbose" mode (-vv), it also outputs any errors returned.  This can be a great way to see which errors result from which conditions on each platform.

The sample library is a full exercise of all the iOS functionality; it's meant to be loaded into an iOS test harness such as the one found in [this project](https://github.com/brotskydotcom/rust-on-ios). While the library can be compiled and linked to on macOS as well, doing so doesn't provide any advantages over the standard macOS tests.

## Client Testing

This crate comes with a "mock" credential store that can be used by clients who want to test without accessing the native platform store.  The mock store is cross-platform and allows mocking errors as well as successes.

## Extensibility

This crate comes with built-in support for the keychain on Mac, the credential manager on Windows, and both secret-service and the kernel keyring on Linux.  But it's also designed to allow clients to "bring their own credential stores" by providing traits that clients can implement.  See the [developer docs](https://docs.rs/keyring/latest/keyring/) for details.

## Dev Notes

* We build using GitHub CI.
* Each tag is built on Ubuntu x64, Win 10 x64, and Mac intel x64.  The `cli` example executable is posted for all platforms with the tag.

### Headless Linux

If you are trying to use keyring on a headless linux box, be aware that there are known issues with getting dbus and secret-service and the gnome keyring to work properly in headless environments.  For a quick workaround, look at how this project's [CI workflow](https://github.com/hwchen/keyring-rs/blob/master/.github/workflows/build.yaml) uses the [linux-test.sh](https://github.com/hwchen/keyring-rs/blob/master/linux-test.sh) script; a similar solution is also documented in the [Python Keyring docs](https://pypi.org/project/keyring/) (search for "Using Keyring on headless Linux systems").  For an excellent treatment of all the headless dbus issues, see [this answer on ServerFault](https://serverfault.com/a/906224/79617).

Note that you can build this crate without secret-service support; this makes the Linux keyring (which works normally on headless linux) the default credential store on Linux platforms.

## License

Licensed under either of

* Apache License, Version 2.0, ([LICENSE-APACHE](LICENSE-APACHE) or http://www.apache.org/licenses/LICENSE-2.0)
* MIT license ([LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT)

at your option.

## Contributors

Thanks to the following for helping make this library better, whether through contributing code, discussion, or bug reports!

- @Alexei-Barnes
- @bhkaminski
- @brotskydotcom
- @complexspaces
- @dario23
- @dten
- @gondolyr
- @hwchen
- @jasikpark
- @jkhsjdhjs
- @jonathanmorley
- @jyuch
- @landhb
- @lexxvir
- @MaikKlein
- @Phrohdoh
- @Rukenshia
- @samuela
- @stankec
- @steveatinfincia
- @Sytten

If you should be on this list, but don't find yourself, please contact @brotskydotcom.

### Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted for inclusion in the work by you, as defined in the Apache-2.0 license, shall be dual licensed as above, without any additional terms or conditions.
