Interrupt fires...
------------------

The keyboard sends signals on its interrupt request line (IRQ), which is mapped
to an ``interrupt vector`` (integer) by the interrupt controller. The CPU uses
the ``Interrupt Descriptor Table`` (IDT) to map the interrupt vectors to
functions (``interrupt handlers``) which are supplied by the kernel. When an
interrupt arrvies, the CPU indexes the IDT with the interrupt vector and runs
the appropriate handler. Thus, the kernel is entered.
