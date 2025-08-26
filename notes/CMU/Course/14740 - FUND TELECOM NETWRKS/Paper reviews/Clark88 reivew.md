Author: Han-Lin (Leo) Chen
Andrew ID: leoc3
Date: Aug 25 2025
### Title: THE DESIGN PHILOSOPHY OF THE DARPA INTERNET PROTOCOLS

### Summary
This paper examines the successes and tradeoffs of the Internet's original design priorities. The datagram model ensured the Internet's core goals of survivability and flexibility, enabling widespread adoption in both military and commercial settings. However, the stateless nature of datagrams makes it difficult to manage resources and ensure accountability. To address these problems, the author proposes the concept of a _flow_—a way to group datagrams into sequences—allowing gateways to maintain "soft state." This idea provides both resilience and improved service management.

### Valid Points
1. Dategram Successes
2. Limitation 
3. Flows

### My Thought on the paper
1. I'm thinking the way we can implement Flow method on datagrams. We can set a boundary with start/end in the header file. However, this might increase the overhead of the datagrams, making the 

### **Citation**

Cerf, V. G., & Clark, D. D. (1988). _The design philosophy of the DARPA Internet protocols_. ACM SIGCOMM Computer Communication Review, 18(4), 106–114. https://doi.org/10.1145/52324.52336


