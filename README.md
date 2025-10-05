# Ducati_HYM939_MY17
The Ducati Hypermotard 939 Model Year 2017 has the Magneti Marelli (rip) IAW 7SM.D0 (HW210) ECU.

Pre-alpha thoughts:

This bike has two diagnostic ports: the yellow one (4 pins: CAN+, CAN-, +12V, GND) and the red one (comes out from the ECU, labeled as "MARELLI COMMUNICATI" and has 3 pins, even if only the left and right pins are actually connected to the ECU, which are GND, K-Line).
In past Ducati bikes, the 3-pin red port (also known as the FIAT diagnostic port) adhered to the FIAT port standard, whose pins were L-Line, GND, and K-Line. K-line is slower than CAN, and in future bikes it will probably be removed.

The IAW 7SM implements the UDS (Unified Diagnostic Services), which allows us to download and upload firmware (where maps are stored), as well as other features that I will not cover here.

To download/upload the firmware, the UDS specifies we need to request the ECU to switch to a specific "session" (a mode, basically), which is the "Programming session".
After having switched to that session type, the bike can't be used normally: the trouble lights on the dashboard start flashing, and the cooling fan starts spinning at high RPM.

Now, the ECU is in a "locked" state. We need to unlock it (many programs refer to this stage as the "login stage"):
In the UDS standard the "login stage" is called "Security Access", which involves asking the ECU for a challenge code, creating a key based on that challenge code, sending it to the ECU, hoping it unlocks.
The algorithm to calculate the key from the challenge code is OEM custom.
