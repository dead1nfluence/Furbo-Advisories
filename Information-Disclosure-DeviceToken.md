#### Affected Entity:

- Furbo Mini
- Furbo 360
	- GATT Characteristic UUID: `C1C1D58F-6CDC-426D-AA4D-5A18BD88FE37`

#### Impact:

An attacker within Bluetooth Low Energy (BLE) range of the Furbo Mini device can read a GATT characteristic that leaks the device’s `DeviceToken`. This token is used to authenticate the device with Furbo’s backend services. If intercepted, an attacker can reuse the `DeviceToken` to re-register the victim’s device to a rogue account, effectively disabling the victim’s access and ability to use their device.

#### Replication Steps:

#### Preparation:

1. Acquire a BLE-capable adapter such as the Nordic nRF52840.
2. Install necessary BLE libraries (e.g., `bleak`, `bluepy`) and obtain the `Furbo_Master.py` script.
3. Ensure proximity to the target Furbo Mini device (within BLE range).

#### Exploitation:

1. Launch the `Furbo_Master.py` script.
2. Scan and connect to the Furbo device shown in the BLE device list.
3. Execute the `reset` command to trigger BLE reinitialization.
4. Execute the `reconnect` command to resume communication.
5. Scan again and reconnect to the re-advertised Furbo device.
6. Execute the `read_device_token` command.
7. Observe the returned Device Token from the BLE characteristic.

8. Get Victim DeviceID (MAC)

9. Factory Reset Attacker Furbo

10. Modify Attacker Furbo MAC to spoof Victim MAC using: sed -i 's/"system\/info\/mac": *"[^"]*"/"system\/info\/mac": "[VICTIM_MAC]"/' /mnt/hw/factory.json

11. Replace Attacker Furbo Device Token located in /mnt/flash/furbo/setup_info.json with Victim Device Token

12. Register Attacker Device following the steps in the App

13. Once Completed Victim DeviceID will be registered to Attacker and Victim's Camera will no longer connect to their account as they aren't the owner of that DeviceID anymore
