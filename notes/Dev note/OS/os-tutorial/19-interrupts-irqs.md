**Goal: Finish the interrupts implementation and CPU timer**

Terminology:

**IRQ** (Interrupt Requests)
An **IRQ** is a signal sent to the **CPU** by hardware or software to indicate that an event needs immediate attention.

Without IRQs, the CPU would have to constantly check every device to see if something happened (called **polling**), which is inefficient.

IRQs allow:
- Fast reaction to events (e.g., key press, mouse click)
- Efficient multitasking
- Real-time responsiveness


**PIC** (**Programmable Interrupt Controller**)
A **PIC (Programmable Interrupt Controller)** is a chip that **manages hardware interrupt signals (IRQs)** and sends them to the **CPU** in a prioritized and orderly way.

- It acts as a **middleman** between devices and the CPU.
- Without it, the CPU would be overwhelmed trying to deal with simultaneous interrupt requests.

**EOI**
EOI is a signal sent to the Programmable Interrupt Controller (PIC) or Advanced Programmable Interrupt Controller (APIC) to indicate that the CPU has finished handling an interrupt and is ready to accept more.
Without sending an EOI, the PIC/APIC may think the interrupt is still being serviced and might not send more interrupts of the same or lower priority.