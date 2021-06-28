# Prototype: Integrating Rust and Swift into an XCode Project

This project demonstrates how Rust and Swift can used together in one XCode
project. Specifically, this XCode project builds a Swift executable which
calls a `say_hey()` function defined in a Rust library.

The Cargo plugin [`cargo-xcode`][1] is used to help wrap this Rust library so
that it can be used from the Swift code. See that project's documentation for
some important configuration directions.

In additional to `cargo-xcode`, some manual wrapping was needed.

Here are this prototype's main elements and some guidance to reproduce:

1. A root XCode project was created. It includes an executable target written
	 in Swift: `HeyRust/main.swift`.
2. A Cargo crate was created and nested within the root project:
	 `HeyRust/rs/Cargo.toml`.
3. The Rust crate's library was marked as `staticlib`.
4. The `cargo-xcode` Cargo plugin was installed locally and executed within the
	 crate via `cargo xcode`. This generated XCode project files.
5. Within the XCode GUI, this `cargo-xcode` generated project was added as a
	 subproject to the root XCode project.
6. Within the XCode GUI, a linker step was added to the root project to include
	 the Cargo crate's output library when linking our Swift executable.
7. A Rust function, `hey_rust()` was written and marked with `#[no_mangle]`.
8. A C header file was created to represent this functio to swift, that is, to
	 declare that this function exists at link time. This was done manually,
	 since this function has a very simple declaration, but this could probably
	 also be created using `cbindgen`.
9. A "bridging header" was added to the root XCode project's group. This is an
	 implicitly-used C header, and we use it to import our above-defined C header
	 file. Its name is of the form `<GroupName>-Bridging-Header.h`; so in our
	 case it is named `TryRust-Bridging-Header.h`.

---

[1]: https://crates.io/crates/cargo-xcode
