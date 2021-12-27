My first open source project is [fxamacker/cbor](https://github.com/fxamacker/cbor). It's a [CBOR codec](https://github.com/fxamacker/cbor#cbor-codec-in-go) used by Arm Ltd., Berlin Institute of Health at Charité, Chainlink, ConsenSys, Dapper Labs, Duo Labs (cisco), EdgeX Foundry, Mozilla, Oasis Labs, Netherlands (govt), Taurus SA, and others.

Other open source contributions include [onflow/atree](https://github.com/onflow/atree), [fxamacker/circlehash](https://github.com/fxamacker/circlehash),  [onflow/cadence](https://github.com/onflow/cadence), and more.

![image](https://user-images.githubusercontent.com/57072051/145697520-4dc89ec2-435b-46f1-8e2c-f9e8ba0ca1df.png)

[Atree](https://github.com/onflow/atree) provides scalable arrays and maps. My design contributions include novel hash collision handling that combines non-cryptographic and cryptographic hashing for faster speed, reduced storage size, and security.  It's different from published designs such as double hashing, 2-choice hashing, cuckoo hashing, etc.  Atree is used by [Cadence](https://github.com/onflow/cadence) in the [Flow Blockchain](https://www.onflow.org/).  Atree wouldn't exist without Dieter Shirley setting goals and inspiring us, Ramtin M. Seraj leading the R&D to make it possible, and Bastian Müller improving Atree while leading the integration into Cadence. Special thanks to Supun Setunga for leading the data migration work and more.

[CircleHash](https://github.com/fxamacker/circlehash) is a family of fast non-cryptographic hash functions I created for speed, ease of audit, and maintainability.  CircleHash64 and CircleHash64fx don't exceed 0.8% worst-bit error in [Strict Avalanche Criterion](https://en.wikipedia.org/wiki/Avalanche_effect#Strict_avalanche_criterion) for 0-128 byte inputs (using demerphq/smhasher modified to test more sizes).  CircleHash64 is used together with BLAKE3 in [onflow/atree](https://github.com/onflow/atree).  Atree and CircleHash were successfully deployed to Flow mainnet on Dec 8, 2021.

I try to balance competing factors such as speed, security, usability, and maintainability based on each project's priorities.

I've been using Go but used other languages extensively (especially C++ in closed source commercial software). I chose Go because it boosted productivity on successful choose-your-language(s) full-stack projects.  With Go, I spent more time creating software and less time debugging compared to multithreaded C++ programming. I miss generics and fast FFI to C functions but I love Go's faster builds, easier concurrency, and increased productivity with Go.  Looking forward to Go 1.18+ having generics.

### Professional Background

I joined Dapper Labs on May 4, 2021. Prior to that, I worked as an independent contractor for ~5 weeks to help Dapper Labs optimize Cadence storage layer and to create a streaming mode branch of fxamacker/cbor.  [Issue 738](https://github.com/onflow/cadence/issues/738) was created on my first day as a contractor and the Cadence team was very welcoming and highly productive to work with.

My prior experience before Dapper Labs includes co-founding & bootstrapping enterprise software company, and working as an IT consultant.

- My smallest consulting client - a startup.  I assisted with prototyping which helped secure their next round of funding.
- My largest consulting client - an S&P 500 company with almost 50,000 employees.  I evaluated (as part of a large team) various technologies to be selected for their new global stack for deployment to over 100 countries.
- My largest software licensing+subscription+support client - a company with over 3,000 employees in the US that deployed my data security system to all their US-based offices and factories.  The tamper-resistant system used 4 types of servers to distribute and enforce security policies across multiple timezones for various client software.  The system was designed to repair and update itself with bugfixes without introducing downtime.  I was only one of two people that conceived, designed, implemented, tested, and maintained the system.  Our system beat enterprise solutions from well-funded large competitors year after year during customer evaluations which included testing employee-attempted data theft.  It was not approved for export or use outside US and Canada.

Developing commercial software provided the advantage of choosing the most appropriate language and framework for each part of the system because the customers didn't know what programming languages, tools, or frameworks were used.
