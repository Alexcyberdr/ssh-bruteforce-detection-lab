# Wazuh SIEM deployment and SSH alert investigation

**Alejandro Báez Santana · Personal SOC lab**

I deployed a Wazuh manager to collect security events from endpoints and used the resulting alerts to investigate SSH authentication failures.

## Wazuh deployment

| Component | Role in my lab |
| --- | --- |
| Wazuh manager on a cloud VPS | Central monitoring system. My recorded allocation was 2 vCPU, 2 GB RAM and 80 GB storage. |
| Linux VPS with Wazuh agent | Monitored endpoint and source of SSH authentication activity. |
| Mac with Wazuh agent | Additional endpoint listed in my monitoring notes. |

### Agent connectivity investigation · January 8, 2026

My deployment notes describe two separate issues. First, the agent's TCP connection to port 1514 timed out. The troubleshooting discussion recorded a TCP listener on the manager and a UDP allowance in the firewall. I needed to check the transport as well as the port number. Second, the discussion recorded an agent/manager version compatibility error. Checking both installed versions was a separate step from checking network reachability. The archived discussion does not establish which change ultimately restored connectivity.

### Evidence and validation

The deployment table and the connectivity investigation are based on historical lab notes. The original manager service output, active-agent listing and Wazuh alert export have not been recovered. The original screenshots linked below document the companion Linux SSH exercise. To verify the Wazuh collection path in a repeat run, capture an active agent ID and a timestamped SSH event carrying that same ID, then match it to the corresponding authentication entry on the Linux host.

## Wazuh investigation

I used the SIEM to review authentication failures, then used a separate Fail2Ban exercise to check how a Linux host responds to repeated failed SSH logins.

| Observation from my Wazuh lab notes | Count |
| --- | ---: |
| Total alerts | 1,952 |
| Alerts classified as authentication failures | 1,333 |

Authentication failures accounted for about 68% of the alerts. These counts describe alerts in my notes. Determining whether someone gained access would require correlating failures with accepted SSH logins on the same host. The original Wazuh dashboard capture and event export are unavailable for this report.

## SSH hardening

My Wazuh lab notes list SSH keys, disabling direct root login, geographic access restrictions, and Fail2Ban among the measures I used. The evidence below comes from my separate Fail2Ban validation on an Ubuntu 22.04 VPS running OpenSSH.

In that exercise, the `sshd` jail tracked repeated failures in `/var/log/auth.log`. Five failures within ten minutes triggered a ban. I checked the ban in `/var/log/fail2ban.log`, verified that SSH access from the test host was blocked, and confirmed connectivity returned after I manually removed the ban.

## Original evidence from the Fail2Ban exercise
[Read the redacted host log excerpts](host-evidence.md).

**Authentication failures.** This capture shows the host-side SSH log used for detection.

![SSH authentication failures in the Linux authentication log](https://raw.githubusercontent.com/Alexcyberdr/fail2ban-ssh-hardening-lab/main/evidence/screenshots/02_AuthLog_SSH_Failed_Attempts.png)

**Recovery check.** This capture documents SSH access after the manual unban.

![SSH access restored after removing the Fail2Ban ban](https://raw.githubusercontent.com/Alexcyberdr/fail2ban-ssh-hardening-lab/main/evidence/screenshots/Fail2Ban_Access_Restored_After_Unban.png)

[All six original screenshots](https://github.com/Alexcyberdr/fail2ban-ssh-hardening-lab/tree/main/evidence/screenshots) · [Log samples](https://github.com/Alexcyberdr/fail2ban-ssh-hardening-lab/tree/main/evidence/logs) · [Fail2Ban configuration](https://github.com/Alexcyberdr/fail2ban-ssh-hardening-lab/blob/main/config/jail.local)
