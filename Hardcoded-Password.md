#### Affected Entity:
- Furbo Mini
- Furbo 360

#### Impact:

The Furbo Mini device contains a hard-coded password for the local `root` user account. This password is stored on a read-only partition in the firmware and ****cannot be modified or reset**** by the device owner.

As a result:

- Any attacker with ***physical access*** to the device and knowledge of the password can connect via ***UART*** (Universal Asynchronous Receiver-Transmitter) and gain ****root shell access****.
- This allows the attacker to bypass this authentication mechanisms, inspect or modify sensitive system components, and potentially extract or tamper with device credentials and firmware.
- The risk is particularly concerning for recycled, second-hand, or disposed devices, as prior users or third parties may retain access indefinitely.
