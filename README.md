On GitHub, I maintain or contribute to projects such as [fxamacker/cbor](https://github.com/fxamacker/cbor), [onflow/atree](https://github.com/onflow/atree), [onflow/ccf](https://github.com/onflow/ccf), [onflow/cadence](https://github.com/onflow/cadence), [onflow/flow-go](https://github.com/onflow/flow-go), etc.

My first open source project was [fxamacker/cbor](https://github.com/fxamacker/cbor). It's used in projects by Arm Ltd., EdgeX&nbsp;Foundry, Flow&nbsp;Foundation, Fraunhofer&#8209;AISEC, IBM, Kubernetes, Linux&nbsp;Foundation, Microsoft, Red Hat, Tailscale, Veraison, [and&nbsp;others](https://github.com/fxamacker/cbor#who-uses-fxamackercbor).

`fxamacker/cbor` passed multiple confidential security assessments in 2022.  A [nonconfidential security assessment](https://github.com/veraison/go-cose/blob/v1.0.0-rc.1/reports/NCC_Microsoft-go-cose-Report_2022-05-26_v1.0.pdf) (prepared by NCC&nbsp;Group for Microsoft&nbsp;Corporation) includes a subset of `fxamacker/cbor` v2.4.0 without finding any vulnerabilities.

Most of the code I wrote is closed source (in many languages but mostly multithreaded C++).  I'm currently enjoying open source projects and the amazing simplicity of Go.

Some of my open source work is described here.

## Design & Implementation


<!-- ![image](https://user-images.githubusercontent.com/57072051/145697520-4dc89ec2-435b-46f1-8e2c-f9e8ba0ca1df.png) -->

<img src="https://github.com/user-attachments/assets/81e44838-e1ab-4282-9c88-762055f412c5" width="720" alt="fxamacker stats in atree">

__[onflow/atree](https://github.com/onflow/atree)__: Atree provides scalable arrays and maps.  It is used by [Cadence](https://github.com/onflow/cadence) in the [Flow Blockchain](https://www.onflow.org/).

Atree segments, encodes, and stores data into chunks of relatively small size.  This enables blockchains to only hash and transmit modified chunks (aka payloads) instead of the entire array, map, or large element.

Among other aspects, I designed and implemented a novel hash collision handling method.  It is different from published methods such as [Cuckoo Hashing](https://en.wikipedia.org/wiki/Cuckoo_hashing), [Double Hashing](https://en.wikipedia.org/wiki/Cuckoo_hashing), [2-Choice Hashing](https://en.wikipedia.org/wiki/2-choice_hashing), etc.

This new hash collision handling method balances speed, security, storage size, etc.  It uses a fast noncryptographic 64-bit hash and if there is a 64-bit hash collision, it uses deferred and segmented 256-bit cryptographic digest (in 64-bit segments to save space).  If there is a collision on the entire combined hash digest, the collision is handled gracefully (not catastrophic).  By default, it uses [CircleHash64f](https://github.com/fxamacker/circlehash) and BLAKE3 for a max combined digest size limit of 320 bits.  Larger digest sizes are possible by replacing BLAKE3 with SHA3-512, etc. for a max digest size limit of 576 bits.  If the max digest size limit is too large, it can be truncated by increments of 64-bits until desired tradeoffs (e.g., collision resistance) are reached.  By design, the cryptographic hash is only computed if there is a collision on the seeded 64-bit fast hash and it is only computed once.

Acknowledgements:  Atree wouldn't exist without Dieter Shirley making priorities clear and inspiring us, Ramtin M. Seraj leading the R&D and empowering us to innovate, and Bastian Müller improving Atree while leading the integration into Cadence. Many thanks to Supun Setunga for the very complex data migration work and more!

## Optimizations

When feasible, my optimizations simultaneously improve speed, memory, storage, and network use without negative tradeoffs.

__[onflow/atree](https://github.com/onflow/atree):__  Designed and implemented [Atree Inlining & Deduplication](https://github.com/onflow/atree/releases/tag/v0.8.0) which was deployed on Sept. 4, 2024. It eliminated over 1 billion mtrie nodes (-61%) and inlined over 500 million payloads to improve memory, storage, and speed on same hardware.

- For example, it reduced peak RAM use by hundreds of GB on each Flow Execution Node, improved Flow transaction speed by ~7%, and also sped up other servers: "btw amazing work this atree inlining, my tinyAN bootstrap time improved like 5x".

__[onflow/flow-go](https://github.com/onflow/flow-go):__  Found optimizations by reading unfamiliar source code and [proposed them](https://github.com/onflow/flow-go/issues/1750#issuecomment-1004870851) to resolve [issue #1750](https://github.com/onflow/flow-go/issues/1750). Very grateful for Ramtin M. Seraj for opening a batch of issues and letting me tackle this one.

[PR #1944](https://github.com/onflow/flow-go/pull/1944) (Optimize MTrie Checkpoint for speed, memory, and file size):
- __SPEED__: 171x speedup (11.4 hours to 4 minutes) in MTrie traversing/flattening/writing phase (without adding concurrency) which led to a 47x speedup in checkpointing (11.7 hours to 15 mins).
- __MEMORY__: -431 GB alloc/op (-54.35%) and -7.6 billion allocs/op (-63.67%)
- __STORAGE__: -6.9 GB file size (without using compression yet)

After [PR #1944](https://github.com/onflow/flow-go/pull/1944) reduced Mtrie flattening and serialization phase to under 5 minutes (which sometimes took 17 hours on mainnet16), creating a separate MTrie state used most of the remaining duration and memory.

Additional optimizations (add concurrency, add compression, etc.) were moved to separate issue/PR and I switched my focus to related issues like [#1747](https://github.com/onflow/flow-go/issues/1747).

UPDATE: About six months later, file size grew from 53GB to 126GB and checkpointing frequency increased to every few hours (instead of about once daily) due to increased transactions and data size.  Without [PR #1944](https://github.com/onflow/flow-go/pull/1944), checkpointing would be taking over 20-30 hours each time, require more operational RAM, and slowdown the system with increased gc pressure.  More info: [issue #2286](https://github.com/onflow/flow-go/issues/2286) and [PR #2792](https://github.com/onflow/flow-go/pull/2792).

## Evaluations and Improvements

__[fxamacker/circlehash](https://github.com/fxamacker/circlehash)__: I created CircleHash64 on weekends after evaluating state-of-the-art fast hashes for work. At the time, I needed a fast hash for short input sizes typically <128 bytes but didn't like existing hashes.  I didn't want to reinvent the wheel so I based it on Google Abseil C++ internal hash.  CircleHash64 is well-rounded: it balances speed, digest quality, and maintainability.

#### CircleHash64 has good results in [Strict Avalanche Criterion (SAC)](https://en.wikipedia.org/wiki/Avalanche_effect#Strict_avalanche_criterion).

|                | CircleHash64 | Abseil C++ | SipHash-2-4 | xxh64 |
| :---           | :---:         | :---:  | :---: | :---: |
| SAC worst-bit <br/> 0-128 byte inputs <br/> (lower % is better) | 0.791% 🥇 <br/> w/ 99 bytes | 0.862% <br/> w/ 67 bytes | 0.802% <br/> w/ 75 & 117 bytes | 0.817% <br/> w/ 84 bytes |

☝️ Using demerphq/smhasher updated to test all input sizes 0-128 bytes (SAC test will take hours longer to run).

#### CircleHash64 is fast at hashing short inputs with a 64-bit seed

|              | CircleHash64<br/>(seeded) | XXH3<br/>(seeded) | XXH64<br/>(w/o seed) | SipHash<br/>(seeded) |
|:-------------|:---:|:---:|:---:|:---:|
| 4 bytes | 1.34 GB/s | 1.21 GB/s| 0.877 GB/s | 0.361 GB/s |
| 8 bytes | 2.70 GB/s | 2.41 GB/s | 1.68 GB/s | 0.642 GB/s |
| 16 bytes | 5.48 GB/s | 5.21 GB/s | 2.94 GB/s | 1.03 GB/s |
| 32 bytes | 8.01 GB/s | 7.08 GB/s | 3.33 GB/s | 1.46 GB/s |
| 64 bytes | 10.3 GB/s | 9.33 GB/s | 5.47 GB/s | 1.83 GB/s |
| 128 bytes | 12.8 GB/s | 11.6 GB/s | 8.22 GB/s | 2.09 GB/s |
| 192 bytes | 14.2 GB/s | 9.86 GB/s | 9.71 GB/s | 2.17 GB/s |
| 256 bytes | 15.0 GB/s | 8.19 GB/s | 10.2 GB/s | 2.22 GB/s |

- Using Go 1.17.7, darwin_amd64, i7-1068NG7 CPU
- Results from `go test -bench=. -count=20` and `benchstat`
- Fastest XXH64 in Go+Assembly doesn't support seed

CircleHash64 doesn't have big GB/s drops in throughput as input size gets larger.  Other CircleHash variants are faster for larger input sizes and a bit slower for short inputs (not yet published).

## Implement IETF Internet Standards (RFC 8949 & RFC 7049)

__[fxamacker/cbor](https://github.com/fxamacker/cbor)__: I designed and implemented a secure CBOR codec after reading RFC 7049.  During implementation, I helped review [the draft](https://github.com/cbor-wg/CBORbis) leading to [RFC 8949](https://datatracker.ietf.org/doc/html/rfc8949).  The CBOR codec rejects malformed CBOR data and has an option to detect duplicate map keys.  It doesn't crash when decoding bad CBOR data.

Decoding 9 or 10 bytes of malformed CBOR data shouldn't exhaust memory. For example,  
`[]byte{0x9B, 0x00, 0x00, 0x42, 0xFA, 0x42, 0xFA, 0x42, 0xFA, 0x42}`

|     | Decode bad 10 bytes to interface{} | Decode bad 10 bytes to []byte |
| :--- | :------------------ | :--------------- |
| fxamacker/cbor<br/>1.0-2.3 | 49.44 ns/op, 24 B/op, 2 allocs/op* | 51.93 ns/op, 32 B/op, 2 allocs/op* |
| ugorji/go 1.2.6 | ⚠️ 45021 ns/op, 262852 B/op, 7 allocs/op | 💥 runtime: out of memory: cannot allocate |
| ugorji/go 1.1-1.1.7 | 💥 runtime: out of memory: cannot allocate | 💥 runtime: out of memory: cannot allocate|

*Speed and memory are for latest codec version listed in the row (compiled with Go 1.17.5).

fxamacker/cbor CBOR safety settings include: MaxNestedLevels, MaxArrayElements, MaxMapPairs, and IndefLength.

## Professional Background

I try to balance competing factors such as speed, security, usability, and maintainability based on each project's priorities.

Most recently, I accepted an offer I received on April 13, 2021 from Dapper Labs. I had been working for them as an independent contractor for about two weeks to help optimize Cadence storage layer and to create a streaming mode branch of fxamacker/cbor.  On my first day as a contractor, I created [issue 738](https://github.com/onflow/cadence/issues/738) and the Cadence team was very welcoming and productive to work with.  I subsequently opened 100+ issues and 100+ PRs at work in 2021.

My prior experience before Dapper Labs includes co-founding & bootstrapping enterprise software company, and working as an IT consultant.

- My smallest consulting client - a startup.  I assisted with prototyping which helped secure their next round of funding.
- My largest consulting client - an S&P 500 company with almost 50,000 employees.  I evaluated (as part of a large team) various technologies to be selected for their new global stack for deployment to over 100 countries.
- My largest software licensing+subscription+support client - a company with over 3,000 employees in the US that deployed my data security system to all their US-based offices and factories.  The tamper-resistant system used 4 types of servers to distribute and enforce security policies across multiple timezones for various client software.  The system was designed to repair and update itself with bugfixes without introducing downtime.  I was only one of two people to ever have access to the source code: just two of us conceived, designed, implemented, tested, and maintained the system.  Our system beat enterprise solutions from well-funded large competitors year after year during customer evaluations which included testing employee-attempted data theft.  It was not approved for export or use outside the US.

Developing commercial software provided the advantage of choosing the most appropriate language and framework for each part of the system because the customers didn't know what programming languages, tools, or frameworks were used.
