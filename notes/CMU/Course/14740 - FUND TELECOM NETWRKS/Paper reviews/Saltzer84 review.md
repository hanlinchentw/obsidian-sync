# End-To-End Arguments in System Design

#### Summary:
The end-to-end argument, a principle for placing functions in layered systems, shows that correctness can be ensured by end-to-end checks and retries, while low-level implementations serve mainly for performance enhancements.
#### Important points
1. Correctness requires end-to-end checks
	- Functions like reliability, security, and duplicate suppression cannot be fully guaranteed by lower layers (e.g., the network).
	- Only the application (endpoints) knows the intended outcome, so end-to-end validation (checksums, retries, acknowledgments) is essential.
	- _Justification_: 
2. Low-level mechanisms are for performance, not correctness
	- Network or OS features (e.g., hop-by-hop checksums, retries) can **reduce error rates** and improve efficiency.
	- But they don’t eliminate the need for end-to-end mechanisms.
	- Their role is **performance optimization**, not correctness.
	- _Justification_: 
3. The end-to-end argument guides system design
	- It acts like Occam’s Razor: don’t push unnecessary functions into lower layers.
	- Helps decide where to place functionality in layered systems (e.g., communication protocols, file systems, operating systems). 
	- Encourages keeping lower layers simple, while applications enforce correctness.
	- _Justification_: 
#### Comments/Questions
#### Citation
	