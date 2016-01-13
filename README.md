## tarpc

tarpc is an RPC framework for rust with a focus on ease of use. Defining and implementing an echo-like server can be done in just a few lines of code:

```rust
#![feature(custom_derive, plugin)]
#![plugin(serde_macros)]
#[macro_use]
extern crate tarpc;
extern crate serde;

rpc_service! {hello_service:
    hello(name: String) -> String;
}

impl hello_service::Service for () {
    fn hello(&self, name: String) -> String {
        format!("Hello, {}!", s)
    }
}

fn main() {
    let server_handle = hello_service::serve("0.0.0.0:0", ()).unwrap();
    let client = hello_service::Client::new(server_handle.local_addr()).unwrap();
    assert_eq!("Hello, Mom!".into(), client.hello("Mom".into()).unwrap());
    drop(client);
    server_handle.shutdown();
}
```

The `rpc_service!` macro generates a module in the current module. In the above example, the module is named `hello_service`. This module will contain a `Client` type, a `Service` trait, and a `serve` function. `serve` can be used to start a server listening on a tcp port. A `Client` can connect to such a service. Any type implementing the `Service` trait can be passed to `serve`. These generated types are specific to the echo service, and make it easy and ergonomic to write servers without dealing with sockets or serialization directly. See the tarpc_examples package for more sophisticated examples.

## Planned Improvements (actively being worked on)

- Automatically reconnect on the client side when the connection cuts out.
- Allow omitting the return type in rpc definitions when the type is `()`.
- Allow users to specify imports inside the `rpc_service!` macro
- Support arbitrary serialization. (currently `serde_json` is used for all serialization)
- Support asynchronous server implementations (currently thread per connection).
- Support doc comments on rpc method definitions