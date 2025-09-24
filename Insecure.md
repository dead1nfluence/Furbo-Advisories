#### Affected entity:

- Furbo Mobile Application

#### Impact:

An attacker who obtains access to the device of a Furbo account owner may be able to retrieve the MFAAuthCode, Cognito Authentication token, as well as P2P authentication codes associated with the account from the device's memory. This information persists on the device after the mobile application has been closed.

#### Replication Steps:

****Preparation:****

1. On a jail broken Android device, connect a web proxy such as Burp Suite or ZAP to monitor the traffic from the Furbo mobile application.
2. Open the application, authenticate.
3. Navigate around the application, connect to a device on your account.
4. Log out of the application.
5. Swipe up and close the application process.
6. In the web proxy history, locate a request to /v5/account/read/login, copy sensitive values from the response body such as the MFA Auth Code.
7. In the web proxy history, locate a request to /v5/device/p2p_connection/get and copy the values from the response body.

****Exploitation:****

1. Ensure Frida has been installed on the device.
2. Download fridump3 from: [https://github.com/rootbsd/fridump3](https://github.com/rootbsd/fridump3)
3. Run: fridump3 -v -u -s Furbo
4. In the output directory created, executed a grep command with partial strings from the response bodies collected in step 6 and 7 of preparation. For example: grep -ri ".eJwFwc26QkAAANB3aWtBfFMsWjD-"
5. Observe that these sensitive details were kept in memory, despite being signed out and the application having been closed.

#### Evidence:

POST /v5/account/read/login HTTP/2  
Host: product.furbo.co  
Authorization: Basic dG9tb2Z1bnJkOmhhcHB5UGV0MTIz  
Content-Type: application/json; charset=UTF-8  
Content-Length: 909  
Accept-Encoding: gzip, deflate, br  
User-Agent: okhttp/4.12.0  
  
  
  
{"Email":"j[REDACTED]m","EncPassword":"iO[REDACTED]\n","MobileId":"73[REDACTED]03","MfaAuthCode":***".eJ[REDACTED]JA"***,
