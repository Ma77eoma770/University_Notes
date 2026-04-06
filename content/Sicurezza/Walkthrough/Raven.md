---
date: 2026-04-04
IP: 192.168.0.47
Difficulty: Easy
Done: true
---
# Recon
## Nmap

![[Pasted image 20260404185150.png]]
## GoBuster

![[Pasted image 20260404185128.png]]

## Nikto

![[Pasted image 20260404190140.png]]

## Word-Press
 
 `wpscan --url http://192.168.0.47/wordpress --enumerate u`
 
![[Pasted image 20260404190116.png]]
# Analysis

## PHPMailer

In /vendor was phpmailer

![[Pasted image 20260404193048.png]]

![[Pasted image 20260404193109.png]]

## SSH

![[Pasted image 20260404195504.png]]

![[Pasted image 20260404195736.png]]

![[Pasted image 20260404195931.png]]

# Exploitation
## Privilege escalation

![[Pasted image 20260404210136.png]]

![[Pasted image 20260404210604.png]]

![[Pasted image 20260404200234.png|697]]

![[Pasted image 20260404211523.png]]

![[Pasted image 20260404212748.png]]