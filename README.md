# Ducati_HYM939_MY17
The Ducati Hypermotard 939 Model Year 2017 has the Magneti Marelli (rip) IAW 7SM.D0 (HW210) ECU.

Pre-alpha thoughts:

This bike has two diagnostic ports: the yellow one (4 pins: CAN+, CAN-, +12V, GND) and the red one (labeled as "MARELLI COMMUNICATI" and has 3 pins, even if only the left and right pins are actually connected to the ECU, which are GND, K-Line).
In past Ducati bikes, the 3-pin red port (also known as the FIAT diagnostic port) adhered to the FIAT port standard, whose pins were L-Line, GND, and K-Line. K-line is slower than CAN, and in future bikes it will probably be removed.

The IAW 7SM firmware implements the UDS (Unified Diagnostic Services), which allows us to download and upload firmware (where maps are stored), as well as other features that I will not cover here. We can send UDS requests either via K-line or via CAN.

To download/upload the firmware, the UDS specifies we need to request the ECU to switch to a specific "session" (a mode, basically), which is the "Programming session".
After having switched to that session type, the bike can't be used normally: the trouble lights on the dashboard start flashing, and the cooling fan starts spinning at high RPM.

Now, the ECU is in a "locked" state. We need to unlock it (many programs refer to this stage as the "login stage"):
In the UDS standard, the "login stage" is referred to as "Security Access", which involves requesting a challenge code from the ECU, generating a key based on that code, and sending it to the ECU, hoping it will unlock.
The algorithm to calculate the key from the challenge code is OEM custom and not public.

---- 

To find what the key algorithm is, we need to reverse the firmware of the ECU. This is not an easy job, so it will take time to find the exact piece of code that does that. I will document my findings here.

First, we need a firmware to decompile. Either we source it from someone who has already read it, or extract it ourselves.
I found a firmware on the internet, but later I discovered that it was corrupted or deliberately missing some key parts, so I chose the hard way.

UDS is locked, so we have to find a lower-level way to read the firmware.

The SoC of the IAW 7SM is a ST10F296(x), so I downloaded its datasheet and studied it. The microcontroller has a BSL (BootStrap Loader) mode, which permits us to load a little (32kB) piece of program before code execution of our choice via the ASC0 interface (connected to K-line/UART)... What if that code read the entire flash of the SoC? Actually, some programs already do this for us, like ST10Flasher. So I won't spend time writing machine code for that startup program, and I will use that tool (linked at the bottom).

So, to read the flash we have to enable that BSL mode. To enable it, we need to pull down the P0L.4 pin so that it samples low on the RESET of the controller.
To do that, we have to open the ECU.

The IAW 7SM has 4 screws, after removing them, the two metal plates are still held together thanks to the 10kg of silicone inside the cracks... So we have to remove it with a silicone remover.
