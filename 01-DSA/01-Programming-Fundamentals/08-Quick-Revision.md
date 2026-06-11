### **Phase 1 & 2: The 30-Second Architect Cheat Sheet**

|**Concept**|**Core Mental Model**|**Architectural Implication**|
|---|---|---|
|**Programming Language**|Human to Computer Communication|Bridges the semantic gap; translates domain logic into machine code.|
|**Procedural**|Function Based|Fast execution, but mutates shared state (hard to scale concurrently).|
|**Functional**|Function + Immutability|Thread-safe by default; perfect for massive parallel data streams.|
|**OOP**|Object Based|Models real-world domains; encapsulates state to prevent data corruption.|
|**Java**|Statically Typed|Types locked at compile-time; ensures safe refactoring and prevents runtime crashes.|
|**Python**|Dynamically Typed|Types resolved at runtime; fast to prototype, but risky at enterprise scale.|
|**Stack Memory**|Local Variables & References|Fast, thread-private, LIFO structure. Cleans itself automatically.|
|**Heap Memory**|Objects|Massive, globally shared memory pool. Requires active management.|
|**Reference**|Stores Memory Address|Lightweight pointer. Java passes a _copy_ of this pointer, not the object itself.|
|**Garbage Collection**|Removes Unused Objects|Daemon thread that reclaims Heap memory by deleting unreachable objects (via GC Roots).|