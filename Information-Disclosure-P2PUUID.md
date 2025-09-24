#### Affected Entity:

- Furbo Mini
- Furbo 360
- 
#### Impact:

An attacker within Bluetooth Low Energy (BLE) range of the Furbo Mini device can issue GATT read requests to a specific characteristic exposed by the device. This characteristic leaks the `p2puuid`—a unique identifier used by the Furbo backend infrastructure for video stream routing. By adding the Victim P2P ID to the attacker device and then removing the default p2p_auth file new credentials to be generated for the victim's p2p ID and the attacker stream's will replace the victim's stream in the victim's mobile app.

#### Replication Steps:

#### Preparation:

1. Acquire a Bluetooth Low Energy adapter (e.g., Nordic nRF52840).
2. Install necessary dependencies (e.g., `bleak`, `bluepy`) and acquire the custom `Furbo_Master.py` script.
3. Ensure the attacker device is within BLE range of the target Furbo Mini.

#### Exploitation:

1. Run `Furbo_Master.py`.
2. Scan and connect to the discovered Furbo BLE device.
3. Execute the `reset` command to trigger a re-initialization.
4. Execute the `reconnect` command to reestablish communication.
5. Scan and connect again to the re-advertised Furbo BLE device.
6. Execute the `read_p2p_uuid` command.
7. Observe the printed response which includes the sensitive `p2puuid`.
8. On a registered attackers device, update MAC Address in /mnt/hw/factory.json to that of the victim's MAC ID (obtained through the BLE MAC address)
9. Update AccountID, DeviceID, DeviceToken, P2PUuid in /mnt/flash/furbo/setup_info.json with Leaked Victim Details
10. Delete /mnt/flash/furbo/p2p_auth.json
11. Reboot the attacker device
12. Observe that the video stream on the victim's mobile app now shows that of the attacker's device.
