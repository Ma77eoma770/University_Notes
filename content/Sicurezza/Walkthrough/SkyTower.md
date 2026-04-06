---
date: 2026-04-04
IP: 192.168.0.48
Difficulty: Easy
Done: true
---
# Recon

## Nmap

![[Pasted image 20260404214525.png]]

## GoBuster

![[Pasted image 20260404214452.png]]

## Nikto

![[Pasted image 20260404215822.png]]
# Analysis

## SQL Injection

![[Pasted image 20260404215556.png]]

![[Pasted image 20260404215617.png]]


> [!bug] It sanitizes the 'OR'
> We can use 'oORr' and comment the rest with #


![[Pasted image 20260404215751.png]]

![[Pasted image 20260404215800.png]]

## SSH


> [!warning] SSH was filtered
> We can use Squid
`nano /etc/proxychains4.conf`
and add 
`http    192.168.0.48    3128 \#SQUID PROXY`

![[Pasted image 20260404221451.png]]

![[Pasted image 20260404221530.png]]

## LinPeas

![[Pasted image 20260404221602.png]]

![[Pasted image 20260404221726.png]]


# Exploitation

## MySQL


> [!bug] Using classic root root mysql login

![[Pasted image 20260404222534.png]]

![[Pasted image 20260404222719.png]]

![[Pasted image 20260404222729.png]]

![[Pasted image 20260404223514.png]]

![[Pasted image 20260404223716.png]]
