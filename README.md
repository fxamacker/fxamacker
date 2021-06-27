My first open source project is [fxamacker/cbor](https://github.com/fxamacker/cbor).  It's used by Arm Ltd., Berlin Institute of Health at Charité, Chainlink, ConsenSys, Dapper Labs, Duo Labs (cisco), EdgeX Foundry, Mozilla, Oasis Labs, and more.

I used Go to create fxamacker/cbor but I've used other languages extensively (especially C++) in closed source software. I chose Go because it saved us a lot of time on a choose-your-language(s) distributed system project.  With Go, I spent more time creating software and less time debugging compared to multithreaded C++ programming.  I miss generics and fast FFI to C functions but I love the fast builds and easy concurrency of Go.

I'm looking forward to trying Zig programming language when it gets closer to release 1.0.  I think Zig can be to C what Rust might be to C++.  A killer feature for Zig would to be for Go to be able to call or embed Zig functions (zgo) without a big FFI overhead.

If you use fxamacker/cbor in your project, please let me know.  I'm curious to see where and how it's used.  Thanks!
