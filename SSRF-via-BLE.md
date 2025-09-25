#### Affected Entity:
- Furbo 360
- Furbo Mini
	- GATT Characteristic UUID: `81F6F485-EAE6-41CC-8A2F-E11A5844A114`

#### Impact:

An attacker within Bluetooth Low Energy (BLE) range of a Furbo Mini device can overwrite the configuration endpoint used by the device to fetch critical files such as `TF_FQDN.json`. By providing a custom URL via the GATT interface, the attacker can cause the Furbo to reach out to a remote attacker-controlled server during setup.

This behavior introduces several risks:

- ***Configuration Tampering:*** The attacker can return malformed or malicious configuration files, disrupting services such as MQTT or P2P.
- ***Denial of Service:** If the returned file is excessively large or malformed, it can fill the device’s storage or break parsing logic, potentially preventing the device from booting or functioning correctly.

#### Replication Steps:
#### Preparation:

1. Acquire a BLE-capable adapter (e.g., Nordic nRF52840).
2. Obtain or build a script (`Furbo_Master.py`) that allows interaction with Furbo's GATT characteristics.
3. Set up an HTTP server to host a crafted `TF_FQDN.json` file (e.g., using Python’s `http.server`).

#### Exploitation:

1. Start the `Furbo_Master.py` script.
2. Scan and connect to the Furbo Mini device.
3. Execute the `send_endpoint` command and specify a URL pointing to your HTTP server.
4. Run the `register_info_ready` command to commit the configuration change.
5. Exit the Python script with the `exit` command.
6. Resume setup of the camera using the official Furbo mobile app.
7. Observe the Furbo device making an HTTP request to your specified endpoint, retrieving and saving the malicious configuration file.
