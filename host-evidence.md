# SSH host evidence from the companion Fail2Ban lab

The following excerpts are reproduced from the redacted samples in my [Fail2Ban lab](https://github.com/Alexcyberdr/fail2ban-ssh-hardening-lab/tree/main/evidence/logs).

### OpenSSH authentication log

```text
Failed password for root from 73.68.xxx.xxx port 50636 ssh2
Failed password for root from 73.68.xxx.xxx port 50639 ssh2
```

### Fail2Ban log

```text
NOTICE [sshd] Ban 73.68.xxx.xxx
```

The two OpenSSH lines record unsuccessful root logins. The Fail2Ban line records a ban by the sshd jail. These excerpts contain no timestamps or Wazuh agent ID, so they cannot independently establish the time between the events or confirm ingestion by the manager.
