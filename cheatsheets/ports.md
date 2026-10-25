# Common Ports
| Port | Proto | Service | Note |
| --- | --- | --- | --- |
| 21 | TCP | FTP | Clear-text file transfer |
| 22 | TCP | SSH | Encrypted shell; brute-force target |
| 23 | TCP | Telnet | Clear-text; red flag |
| 25 | TCP | SMTP | Mail sending |
| 53 | UDP/TCP | DNS | Name resolution; tunneling abuse |
| 67/68 | UDP | DHCP | Address leasing |
| 80 | TCP | HTTP | Clear-text web |
| 110 | TCP | POP3 | Mail download |
| 135 | TCP | MS RPC | Windows RPC; lateral movement |
| 139 | TCP | NetBIOS | Legacy file sharing |
| 143 | TCP | IMAP | Mail access |
| 443 | TCP | HTTPS | Encrypted web |
| 445 | TCP | SMB | File sharing; worm/ransomware vector |
| 3389 | TCP | RDP | Remote Desktop; brute-force & ransomware entry |
