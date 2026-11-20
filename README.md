# ssh-bruteforce-detection-lab
Detection and analysis of real-world SSH brute-force attempts using Linux auth logs.

## Wazuh investigation

I monitored a Linux VPS and a Mac with Wazuh agents. My Wazuh manager ran with two virtual CPUs, 2 GB of RAM, and 80 GB of storage. I used the SIEM to review authentication failures, then used a separate Fail2Ban exercise to check how a Linux host responds to repeated failed SSH logins.

| Observation from my Wazuh lab notes | Count |
| --- | ---: |
| Total alerts | 1,952 |
| Alerts classified as authentication failures | 1,333 |

Authentication failures accounted for about 68% of the alerts. These counts describe alerts in my notes. Determining whether someone gained access would require correlating failures with accepted SSH logins on the same host. The original Wazuh dashboard capture and event export are unavailable for this report.

## SSH hardening

My Wazuh lab notes list SSH keys, disabling direct root login, geographic access restrictions, and Fail2Ban among the measures I used. The evidence below comes from my separate Fail2Ban validation on an Ubuntu 22.04 VPS running OpenSSH.

In that exercise, the `sshd` jail tracked repeated failures in `/var/log/auth.log`. Five failures within ten minutes triggered a ban. I checked the ban in `/var/log/fail2ban.log`, verified that SSH access from the test host was blocked, and confirmed connectivity returned after I manually removed the ban.

## Original evidence from the Fail2Ban exercise

**Authentication failures.** This capture shows the host-side SSH log used for detection.

![SSH authentication failures in the Linux authentication log](https://raw.githubusercontent.com/Alexcyberdr/fail2ban-ssh-hardening-lab/main/evidence/screenshots/02_AuthLog_SSH_Failed_Attempts.png)

**Recovery check.** This capture documents SSH access after the manual unban.

![SSH access restored after removing the Fail2Ban ban](https://raw.githubusercontent.com/Alexcyberdr/fail2ban-ssh-hardening-lab/main/evidence/screenshots/Fail2Ban_Access_Restored_After_Unban.png)

[All six original screenshots](https://github.com/Alexcyberdr/fail2ban-ssh-hardening-lab/tree/main/evidence/screenshots) · [Log samples](https://github.com/Alexcyberdr/fail2ban-ssh-hardening-lab/tree/main/evidence/logs) · [Fail2Ban configuration](https://github.com/Alexcyberdr/fail2ban-ssh-hardening-lab/blob/main/config/jail.local)
