# End-To-End Arguments in System Design

#### Summary:
The end-to-end argument, a principle for placing functions in layered systems, shows that correctness can be ensured by end-to-end checks and retries, while low-level implementations serve mainly for performance enhancements.
#### Important points
1. Low-level mechanisms (like checksums, retries, error detection) cannot guarantee correctness, but they can improve performance by reducing error frequency. True correctness requires end-to-end checks and retries at the application level.
	_Justification_: This point is important 
		- The file transfer example shows that many threats exist at different layers (disk, memory, software, network, crashes).
		-  Low-level mechanisms help but cannot cover all threats. An **end-to-end checksum + retry** provides correctness at minimal cost.
		- Since low-level systems are shared across applications, forcing reliability features there can burden apps that don’t need them.
		- This highlights the trade-off: low-level functions should be added only if they clearly improve **performance**, not correctness.
2. The correct placement of functions varies with the application. What’s useful for one case (e.g., live conversation) may harm another (e.g., recorded messages).
	_Justification_: 
	-  Prevents misapplication of the principle.
	- Shows that the argument is a guideline, not a rigid rule.
	- Forces designers to consider application semantics (e.g., latency vs. accuracy) rather than blindly enforcing reliability at one layer.
	- Encourages nuanced engineering decisions that fit the actual end requirements.
3. History shows repeated rediscovery — it’s a general systems principle
	The end-to-end argument keeps reappearing in different domains: networking, file storage, database commits, banking audits, airline reservations, tape backup, OS kernels, even RISC architecture.
	_Justification_: 
	- Demonstrates the generality of the principle: correctness belongs at the endpoints in any layered system.
	- Historical failures (like corrupted tape systems or network gateways) show the cost of ignoring it.
	- Provides a unifying way to think across fields — whether networking, operating systems, or storage, the same principle applies.
	- Reinforces that it’s not just theory: it solves practical, recurring problems in real-world systems.
#### Comments/Questions
#### Citation
	