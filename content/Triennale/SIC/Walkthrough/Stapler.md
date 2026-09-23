---
date: 2026-04-05
IP: 192.168.0.50
Difficulty: Easy/Medium
Done: true
---
# Recon

## Nmap

![[Pasted image 20260405112022.png]]

![[Pasted image 20260405115651.png]]
## GoBuster

![[Pasted image 20260405111958.png]]
# Analysis

## FTP

> [!bug] Nonymous login in FTP

![[Pasted image 20260405112955.png]]

![[Pasted image 20260405112912.png]]

![[Pasted image 20260405112926.png]]


> [!done] So now we have:
> - Harry
> - Elly
> - John

## SMB

![[Pasted image 20260405113611.png]]

![[Pasted image 20260405113919.png]]

![[Pasted image 20260405113901.png]]

![[Pasted image 20260405114030.png]]

![[Pasted image 20260405114102.png]]

> [!warning] There was some random stuff that we dont need

## Other site

![[Pasted image 20260405192418.png]]

![[Pasted image 20260405192429.png]]

> [!success] Now we have:
> - Harry
> - Elly
> - John
> - Kathy
> - Abby

## PHP Login

![[Pasted image 20260405192503.png]]

## Word-Press

![[Pasted image 20260405193716.png]]

![[Pasted image 20260405193703.png]]
# Exploitation

## WP-BruteForcing

![[Pasted image 20260405194229.png]]

![[Pasted image 20260405195628.png]]

> [!bug] Another Rabbit Hole

## WP-Plugin

> [!bug] Found a plugin that was vulnerable and used a tweaked exploit

![[Pasted image 20260405201939.png]]

![[Pasted image 20260405202318.png]]

![[Pasted image 20260405203302.png]]

![[Pasted image 20260405203312.png]]

![[Pasted image 20260405203431.png]]

> [!bug] Can't use that because the file isn't writable

> [!success] We can access a reverse shell using the same shell and with `nc -lvnp 1234`

## Privilege escaltion

![[Pasted image 20260405204822.png]]

> [!bug] We find a vulnerability in linux version

![[Pasted image 20260405212546.png]]

![[Pasted image 20260405212647.png]]