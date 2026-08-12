# Attack N: RDP brute-force attack

## Objective

Simulate an RDP brute-force/password-guessing attack against a Windows 11 endpoint and investigate the resulting authentication events in Splunk.
The objective of this attack is to demonstrate how repeated failed RDP authentication attempts can be detected and correlated with a subsequent successful login.

## Environment

- **Attacker:** Kali (192.168.10.100)
- **Target:** Win11 (192.168.10.11)
- **Detection point:** Splunk (192.168.10.10)

## Attack Steps

1. Prepare the Password List:
   
A password dictionary named password.txt was used as the wordlist for the brute-force attempt.
The target account was: jsmith

2. Launch the RDP Brute-Force Attack:

From the Kali Linux attacker machine (192.168.10.100), Hydra was used to attempt authentication against the Windows 11 RDP service.
```
hydra -l jsmith -P password.txt rdp://192.168.10.11
Where:

-l jsmith specifies the target username.
-P password.txt specifies the password wordlist.
rdp://192.168.10.11 specifies the RDP service and target

```
3. Successful Authentication:

The attack eventually discovered the correct password for the jsmith account: Apple123
The successful password allowed authentication to the Windows 11 target through RDP.

Lab note: The password above was intentionally created for this isolated SOC lab environment and should not be reused for real accounts.

## Detection

The attack was investigated using Splunk by searching the Windows authentication events received from the endpoint.

1. Search for the Target User:

The initial Splunk search was:  "index=endpoint jsmith" 
This search was used to identify authentication-related events associated with the jsmith account.
The results showed multiple events associated with the account, which provided the starting point for investigating the suspected brute-force activity.

2. Identify Failed Logon Attempts:

The investigation was narrowed to Windows failed authentication events using Event ID 4625.
Splunk search was: "index=endpoint jsmith EventCode=4625"
Event ID 4625 represents a failed logon attempt.
The search revealed approximately 25 failed authentication attempts within a short period of time.
This behavior was considered suspicious because a large number of failed authentication attempts against the same account in a short time period is consistent with password guessing/brute-force activity.

Key fields reviewed during the investigation included:

Account_Name, EventCode, Source_Network_Address, Computer_Name, Logon_Type, Timestamp

3. Identify the Source of the Attack:

To determine whether the failed attempts originated from the Kali attacker machine, the investigation was then focused on successful Windows authentication events.
The search was changed to Event ID 4624, which represents a successful logon:
Splunk search was: "index=endpoint jsmith EventCode=4624
The resulting event showed that the successful authentication originated from the Kali machine: "192.168.10.100"

This was significant because 192.168.10.100 was the known IP address of the attacker machine in the lab.
The successful authentication correlated with the previous failed authentication attempts, providing evidence of the complete attack sequence


**Key indicators observed:**
 - Event ID(s) relevant to this attack:
   1.EventCode=4625 (Failed logon events)
   2.Eventcode=4624 (Successful logon event)
 - Fields that matter (account name, source IP, process name, etc.)
   account name - kali
   source IP - 192.168.10.100
   process name -
   protocol - RDP
   tool - hydra
  

## Screenshots

* [Attack – Hydra RDP Brute-Force](screenshots/Attack-rdp-1.png)
* [Detection – Failed Logons](screenshots/Detection-2.png)
* [Detection – Splunk Investigation](screenshots/Detection-3.png)
* [Detection – Splunk Investigation](screenshots/Detection-4.png)
* [Detection – Splunk Investigation](screenshots/Detection-5.png)
* [Detection – Successful Logon](screenshots/Detection-6.png)


## MITRE ATT&CK Mapping

## MITRE ATT&CK Mapping

| Tactic            | Technique                      | ID        | Description                                                                |
| ----------------- | ------------------------------ | --------- | -------------------------------------------------------------------------- |
| Credential Access | Brute Force: Password Guessing | T1110.001 | Hydra was used to repeatedly attempt passwords against the target account. |
| Lateral Movement  | Remote Services: RDP           | T1021.001 | RDP was the remote service targeted by the attack.                         |


## Lessons Learned 

This attack demonstrated how an RDP brute-force attack can be identified through Windows authentication logs.
The most important indicators were the large number of Event ID 4625 failures within a short period, followed by a successful Event ID 4624 authentication from the same source.
The investigation also demonstrated the importance of correlating multiple events instead of relying on a single authentication failure.
Detection Improvements
For a production environment, this behavior could be used to create an automated Splunk detection that alerts when:
A single source generates a high number of failed logons.
Multiple failed logons target the same account.
A successful login occurs shortly after numerous failures.
The source IP is unusual or has not previously accessed the endpoint.
RDP authentication attempts originate from an unexpected network segment.

## Recommendation

- Create a Splunk alert for multiple Event ID 4625 failures from the same source IP.
- Investigate a 4624 successful login if it happens after many failed login attempts.
- Restrict RDP access to trusted machines or networks.
- Use strong passwords and account lockout policies to reduce brute-force attacks.

## Conclusion

The lab successfully simulated an RDP brute-force attack against the Windows 11 endpoint.
Hydra generated repeated authentication attempts against the jsmith account, resulting in approximately 25 failed logon events (4625) before the correct password was discovered.
Splunk was then used to investigate the authentication activity. The failed events were correlated with a successful logon event (4624) originating from the Kali Linux attacker machine (192.168.10.100).

