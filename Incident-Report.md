# Linux Log Analysis Incident Report

## Incident Summary

This investigation analyzed a Linux authentication log (`auth.log`) to identify evidence of unauthorized SSH login attempts. Multiple failed authentication attempts were observed from external IP addresses targeting privileged accounts. The investigation also confirmed one successful login by a legitimate internal user and verified subsequent administrative activity.

---

# Investigation Objectives

- Identify failed SSH authentication attempts
- Determine the source IP addresses
- Identify targeted user accounts
- Verify successful logins
- Identify administrative (sudo) activity
- Determine whether the host was compromised

---

# Evidence Collected

| Evidence | Description |
|----------|-------------|
| auth.log | Linux authentication log containing SSH and sudo events |

---

# Investigation Timeline

| Time | Event |
|------|-------|
| 08:12:01 | Failed SSH login from 203.0.113.45 |
| 08:12:20 | Successful login by analyst |
| 08:13:01 | Analyst executed sudo apt update |
| 08:15:44 | Failed root login from 198.51.100.88 |
| 08:16:02 | SSH connection closed |

---

# Investigation Steps

## Step 1 — Review Authentication Logs

The authentication log was reviewed using:

```bash
tail -20 auth.log
head -20 auth.log
```

This provided an overview of recent authentication activity.

---

## Step 2 — Identify SSH Activity

SSH-related events were extracted using:

```bash
grep ssh auth.log
```

This isolated all SSH authentication events.

---

## Step 3 — Review Failed Logins

Failed authentication attempts were identified with:

```bash
grep "Failed password" auth.log
```

Seven failed SSH login attempts were identified.

---

## Step 4 — Review Successful Logins

Successful authentication events were extracted using:

```bash
grep "Accepted password" auth.log
```

One successful login was identified.

---

## Step 5 — Review Administrative Activity

Administrative actions were reviewed using:

```bash
grep sudo auth.log
```

One legitimate sudo command was executed by the analyst account.

---

## Step 6 — Identify Targeted Accounts

Targeted usernames were extracted with:

```bash
grep "invalid user" auth.log
```

Attackers attempted to authenticate using:

- admin
- root

---

## Step 7 — Extract IP Addresses

IP addresses were extracted using:

```bash
grep -oE '([0-9]{1,3}\.){3}[0-9]{1,3}' auth.log | sort | uniq
```

Observed IP addresses:

- 203.0.113.45
- 198.51.100.88
- 10.0.0.15

---

## Step 8 — Count Failed Login Attempts

The number of failed logins was calculated using:

```bash
grep "Failed password" auth.log | wc -l
```

Result:

```
7
```

---

## Step 9 — Identify Most Active Attacker

Failed login attempts by source IP were counted using:

```bash
grep "Failed password" auth.log | awk '{print $(NF-3)}' | sort | uniq -c
```

Results:

| IP Address | Failed Attempts |
|------------|----------------|
| 203.0.113.45 | 5 |
| 198.51.100.88 | 2 |

---

# Findings

The investigation identified repeated SSH brute-force attempts against administrative accounts.

Attackers attempted to authenticate using both **admin** and **root** accounts.

A legitimate analyst successfully authenticated from an internal IP address and executed an authorized system update using sudo.

No evidence of successful unauthorized access or privilege escalation was identified.

---

# Indicators of Compromise (IOCs)

### Malicious IPs

- 203.0.113.45
- 198.51.100.88

### Legitimate Internal Host

- 10.0.0.15

### Targeted Accounts

- admin
- root

---

# MITRE ATT&CK Mapping

| Technique | ID |
|-----------|----|
| Brute Force | T1110 |
| Valid Accounts | T1078 |

---

# Conclusion

The observed activity is consistent with an SSH brute-force attack targeting administrative accounts.

Although multiple authentication attempts were detected, no evidence indicates that the attackers successfully gained access to the system.

The successful login and subsequent sudo activity originated from an authorized internal user and represent legitimate administrative actions.

Based on the available evidence, the incident is classified as an unsuccessful SSH brute-force attack. No indicators suggest successful authentication by the attackers or compromise of the target system.