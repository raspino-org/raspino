<div align="center"><a href="https://raspino.org">
  <img src="/resources/images/raspino.png" alt="Raspino Logo" width="260"></a>
</div>
<br>  

---
📣 gpiodWrapV1 [0.1.2] 2026-11-08

Hotfix & Stability Patch for Thread Synchronization and Lifecycle

Today's focus: Constructor, Destructor, Signal Handling, and the Watchdog Thread.

Fixed

Watchdog Thread (Program Termination Blockage Resolved): A logic error in the watchdog's while loop has been fixed. The thread was not correctly linked to its running flag, causing programs that normally terminated to hang in the background at the end of `main()`.

Race Condition on Termination Fixed: The main thread (destructor) and background thread (watchdog) are now properly synchronized. It is now system-wide impossible for both instances to execute `closeChip()` simultaneously or sequentially. This completely eliminates the duplicate execution of the cleanup logic.

Optimized

Constructor Structure: The watchdog thread is now completely inactive when signal handling is disabled.  Future versions are planned to allow the watchdog to take on additional monitoring tasks and be active by default.

Destructor & Signal Lifecycle:

Scenario A (Regular Termination): If a program runs to completion, the destructor deactivates the watchdog in a controlled manner and executes `closeChip()` (safe-by-default).

Scenario B (Abort via Ctrl+C): If the program is hard-terminated, the watchdog intercepts the signal, immediately puts all lines into a safe state (safe-by-default), and cleanly returns the signal to the operating system via POSIX `raise()` (`^C`).

Important Note: Without a signal handler, the GPIO lines cannot be reset to a safe input state during a hard termination.  

<br>
---
🚨 gpioWrapV1 [v0.1.2] — Hotfix & Stability Patch

A logic error in the `while` loop of the watchdog has been fixed. The thread was not correctly linked to its running flag, which caused programs that normally terminated to hang at the end.

The main thread and watchdog are now properly synchronized. It is now system-wide impossible for both instances to perform cleanup simultaneously or sequentially.


The watchdog is now completely inactive when signal handling is disabled.  But it is prepared for future versions to take on further monitoring tasks and will then be active by default.</p>

<br>

<h4>Constructor Structure:</h4>

<p>If a program runs to completion, the destructor deactivates the watchdog in a controlled manner and initiates cleanup.</p>

<p>If the program is hard-canceled, the watchdog intercepts the signal, immediately puts all lines into the safe "Safe-by-Default" state, and cleanly returns the signal to the operating system via POSIX "raise()" "^C".</p>

<br>

<h4>Important Note:</h4>

<p>Without a signal handler, the GPIO lines cannot be reset to the safe input state in the event of a hard termination.</p>
<br>
<div align="center"><a href="https://github.com/raspino-org/gpiodWrapV1">**gpiodWrapV1 [0.1.2]**</a>
</div>
<br>

---
📢 gpiodWrapV1 [v0.1.0] — Official Release

Our new flagship, <a href="https://github.com/raspino-org/gpiodWrapV1">gpioWrapV1</a> [v0.1.0], is now officially available in its inaugural version. While we continue to develop gpiodWrap [v2.0] in parallel, V1 offers a powerful preview of the architectural direction we are heading.
As a hardware-oriented wrapper, raw performance is paramount. Consequently, we have entirely eliminated the use of maps and vectors. Our concern is not with the data structures themselves, but rather with preventing frequent heap allocations—which we aim to eliminate entirely from the hot paths.
By allocating a dedicated, fixed memory region for each GPIO line, the framework requires no additional runtime memory allocation. This architectural choice yields significant advantages: the CPU can rapidly access optimized structures that are fetched into the cache in a single operation, bypassing the overhead and complex pointer manipulation typically involved in dynamic lookups.
The result is direct, safe, and predictable access to the hardware registers in just a few CPU clock cycles.

I look forward to hearing your opinions and suggestions.

---
**gpiod vs esp32**

No, it wasn’t a case of working against each other; it was a case of working together.
I’ve conducted a few tests and would like to share them with you.  
Test setup: an ESP32 sends a signal—with a period of one microsecond—to an event line on my Raspberry Pi 5.  

The `gpioWrap` callback then forwards these edges to a pin configured as an output. The offset between the two oscilloscope channels (monitoring the ESP32 and the Pi 5 output) is 23 microseconds.  

To make things a bit more interesting, I also maxed out `stress-ng --cpu` across all cores. There is no jitter and no change in the offset.  

Take a look at the images—the results are fantastic.

---
🇩🇪 **C++ auf dem Raspberry Pi, so einfach wie Arduino**

Raspino bildet den Rahmen für unsere Open-Source-Repositories auf GitHub. Wir machen die hardwarenahe Programmierung mit C++ auf dem Raspberry Pi unkompliziert, zugänglich und direkt.

🇬🇧 **C++ on the Raspberry Pi, as easy as Arduino**  


Raspino provides the framework for our open-source repositories on GitHub. We make low-level programming with C++ on the Raspberry Pi straightforward, accessible and direct.

---

## 📚 Raspino Repositories
**Public**  
- <a href="https://github.com/raspino-org/gpiodWrap">gpiodWrap</a> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<sub>Works with libgpiod 2.x</sub>
- <a href="https://github.com/raspino-org/gpiodWrapV1">gpiodWrapV1</a> <sub>Works with libgpiod 1.6.x</sub>
- <a href="https://github.com/raspino-org/mcp23017" alt="mcp23017">mcp23017</a>

**Comming soon**  
- RaspinoCore
  
---

## ⚖️ Disclaimer
** 🇩🇪 **
Raspino ist eine zum Schutz angemeldete Marke und gehört der Community. Die Verwendung des Namens unterliegt unseren Markenrichtlinien.  

⚠️ Wichtige Hinweise (Haftungsausschluss):
Die Bereitstellung der Software erfolgt "wie besehen" (as is) und ohne jegliche ausdrückliche oder implizite Gewährleistung. Die Nutzung des Codes und der Hardware-Ansteuerungen erfolgt vollständig auf eigene Gefahr.  

Die Raspino Open-Source-Initiative und ihre Repositories stehen in keiner Verbindung zur Raspberry Pi Foundation oder Arduino AG. Es handelt sich um eine unabhängige Entwicklung, die von den genannten Marken weder gesponsert noch unterstützt oder autorisiert wird. 


** 🇬🇧 **
Raspino is a pending trademark and belongs to the community. Any use of the name is subject to our trademark guidelines.  

⚠️ Important Notice (Disclaimer):  
The software is provided "as is" and without any express or implied warranty. Use of the code and hardware controls is entirely at your own risk.

The Raspino Open-Source Initiative and its repositories are not affiliated with the Raspberry Pi Foundation or Arduino AG. This is an independent development and is neither sponsored, endorsed, nor authorized by the aforementioned brands. All brand names and trademarks used are the property of their respective owners and are used here solely for reference or comparison purposes.
