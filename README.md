My first open source project is [fxamacker/cbor](https://github.com/fxamacker/cbor). It's a [CBOR codec](https://github.com/fxamacker/cbor#cbor-codec-in-go) used by Arm Ltd., Berlin Institute of Health at Charité, Chainlink, ConsenSys, Dapper Labs, Duo Labs (cisco), EdgeX Foundry, Mozilla, Oasis Labs, Netherlands (govt), Taurus SA, and others.

Other open source contributions include [onflow/atree](https://github.com/onflow/atree), [fxamacker/circlehash](https://github.com/fxamacker/circlehash),  sometimes [onflow/cadence](https://github.com/onflow/cadence), and others.

![image](https://user-images.githubusercontent.com/57072051/145697520-4dc89ec2-435b-46f1-8e2c-f9e8ba0ca1df.png)

[Atree](https://github.com/onflow/atree) provides scalable arrays and maps. My design contributions include novel hash collision handling that combines non-cryptographic and cryptographic hashing for faster speed, reduced storage size, and security.  It's different from published designs such as double hashing, 2-choice hashing, cuckoo hashing, hopscotch hashing, etc.

[CircleHash](https://github.com/fxamacker/circlehash) is a family of fast non-cryptographic hash functions I created.  CircleHash64 and CircleHash64fx don't exceed 0.8% worst-bit error in [Strict Avalanche Criterion](https://en.wikipedia.org/wiki/Avalanche_effect#Strict_avalanche_criterion) for 0-128 byte inputs (using demerphq/smhasher modified to test more sizes).

I try to balance competing factors such as speed, simplicity, security, usability, and maintainability based on each project's priorities.

I've been using Go but used other languages extensively (especially C++). I chose Go because it boosted productivity on successful choose-your-language(s) full-stack projects.  With Go, I spent more time creating software and less time debugging compared to multithreaded C++ programming. I miss generics and fast FFI to C functions but I love Go's faster builds, easier concurrency, and increased productivity with Go.  Looking forward to Go 1.18 having generics.
