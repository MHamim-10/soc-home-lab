# Attack N: <Attack Name>

## Objective

What technique is being simulated and why (e.g. tie it to a MITRE ATT&CK technique ID if applicable, like `T1110 - Brute Force`).

## Environment

- **Attacker:** Kali (192.168.10.100)
- **Target:** <machine name / IP>
- **Detection point:** Splunk (192.168.10.10)

## Attack Steps

1. Step-by-step commands/tools used, with the actual command-line syntax.
2. Include any obstacles hit and how they were resolved (this is often the most valuable part for readers).

```
<command(s) used>
```

## Detection

The SPL search(es) used to identify this activity in Splunk:

```spl
<your search here>
```

**Key indicators observed:**
- Event ID(s) relevant to this attack
- Fields that matter (account name, source IP, process name, etc.)

*(Screenshot of Splunk results here — see /screenshots)*

## MITRE ATT&CK Mapping

| Tactic | Technique |
|---|---|
| e.g. Credential Access | T1110 - Brute Force |

## Lessons Learned / Notes

Anything unexpected, troubleshooting done, or ideas for follow-up detections (e.g. alerting rules, lockout policies).
