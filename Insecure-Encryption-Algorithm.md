#### Affected Entity:
- Furbo 360
- Furbo Mini
	- File: `/etc/shadow`

#### Impact:
The Furbo Mini device stores the root user password in `/etc/shadow` using ****DES-based hashing****. DES (Data Encryption Standard) is a deprecated encryption method known to be ****cryptographically weak**** due to its:
- Small key size (56-bit),
- Susceptibility to brute-force attacks,
- Vulnerability to modern cryptanalysis.

Because of this weak encryption, an attacker with access to the device's filesystem (e.g., via UART or firmware extraction) can easily ****brute-force**** the root password hash and obtain cleartext credentials using commonly available tools like ****Hashcat****.

This allows an attacker to:
- Bypass all local authentication protections,
- Access or manipulate sensitive system data,
- Escalate privileges or modify the device’s behavior.
  
#### Replication Steps:
#### Preparation:
1. Extract the contents of `/etc/shadow` from the device.
2. Save the root password hash to a file named `hash.txt`.
#### Exploitation:
1. Run the following Hashcat command:
`hashcat -m 1500 -w 3 -a 3 hash.txt '?a?a?a?a?a?a?a?a'`
2. Wait for the hash to be cracked.
