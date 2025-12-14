# hAP ax3 Security

Here's how I secured the main router/firewall in my homelab. Most of these configuration changes were made based on the RouterOS manual section _Securing Your Router_

## Changed access username from default

This adds a second factor of uncertainty for an attacker to gain unauthorized access. They would need to brute-force both the username and the password. Despite the username alone being easily susceptible to dictionary attack, having to guess both a list of common usernames _and _ passwords adds considerable time and resources needed for a brute-force attack.
