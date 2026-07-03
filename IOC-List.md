# Indicators of Compromise (IOCs)

## Summary

The investigation identified multiple failed SSH authentication attempts originating from two external IP addresses targeting administrative accounts. No evidence of successful unauthorized access or system compromise was identified.

---

## Malicious IP Addresses

| IP Address | Description |
|------------|-------------|
| 203.0.113.45 | Multiple failed SSH login attempts targeting invalid accounts. |
| 198.51.100.88 | Failed SSH login attempts targeting the root account. |

---

## Legitimate Internal Host

| IP Address | Description |
|------------|-------------|
| 10.0.0.15 | Successful login by the legitimate analyst account. |

---

## Targeted Usernames

| Username | Notes |
|----------|-------|
| admin | Invalid account targeted during brute-force attempts. |
| root | Administrative account targeted during login attempts. |

---

## SSH Client Ports Observed

- 50322
- 50324
- 50328
- 50331
- 50336
- 51522
- 60211
- 60213
- 60215

---

## Summary of Findings

- 7 failed SSH authentication attempts observed.
- 2 external IP addresses involved.
- 1 successful login from an internal host.
- 1 legitimate sudo command executed after successful authentication.
- No evidence of successful compromise.

---

## MITRE ATT&CK Mapping

| Technique | ID | Description |
|-----------|----|-------------|
| Brute Force | T1110 | Multiple failed SSH authentication attempts targeting privileged accounts. |
| Valid Accounts | T1078 | Legitimate user authentication was observed and verified as authorized activity. |