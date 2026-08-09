---
date: 2026-04-09
IP: 192.168.0.47
Difficulty: Easy/Medium
Done: true
---
# Recon

## Nmap

![[Pasted image 20260409214235.png]]

![[Pasted image 20260409214714.png]]

# Analysis


> [!bug] We now a bug from OpenSSH < 7.7 (Timed Attack)

![[Pasted image 20260409214732.png]]

![[Pasted image 20260409215048.png]]

![[Pasted image 20260409220401.png]]

![[Pasted image 20260409220416.png]]

![[Pasted image 20260409220427.png]]

![[Pasted image 20260409220531.png]]

![[Pasted image 20260410204825.png]]

![[Pasted image 20260410204837.png]]


> [!bug] There is a login probably vulnerable from sql injection

![[Pasted image 20260410211126.png]]

> [!bug] We now know that the login is vulnerable to a time based attack

![[Pasted image 20260410211258.png]]

![[Pasted image 20260410213301.png]]

![[Pasted image 20260410212804.png]]


![[Pasted image 20260410214505.png]]

![[Pasted image 20260410220006.png]]

![[Pasted image 20260410213116.png]]

> [!success] Smeagol worked in ssh!

![[Pasted image 20260410213845.png]]

![[Pasted image 20260410214055.png]]


> [!bug] Copied the hashed sql password for root without the "*"

![[Pasted image 20260410220053.png]]

![[Pasted image 20260410220026.png]]

![[Pasted image 20260410221047.png]]

# Exploitation

# Post-Exploitation