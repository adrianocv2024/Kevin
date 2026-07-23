root@ip-10-130-177-181:~# ssh -i ~/.ssh/id_ed25519 -p 2222 teste@127.0.0.1
ssh: connect to host 127.0.0.1 port 2222: Connection refused
root@ip-10-130-177-181:~# ^C
root@ip-10-130-177-181:~# sudo systemctl daemon-reload
root@ip-10-130-177-181:~# sudo systemctl restart ssh.socket
root@ip-10-130-177-181:~# sudo systemctl restart ssh
root@ip-10-130-177-181:~# ss -tulpn | grep ssh
tcp   LISTEN 0      4096               0.0.0.0:22         0.0.0.0:*    users:(("sshd",pid=12754,fd=3),("systemd",pid=1,fd=92))  
tcp   LISTEN 0      4096                  [::]:22            [::]:*    users:(("sshd",pid=12754,fd=4),("systemd",pid=1,fd=93))  
root@ip-10-130-177-181:~# ss -tulpn | grep 2222
root@ip-10-130-177-181:~# sudo systemctl stop ssh.socket
sudo systemctl disable ssh.socket
Removed "/etc/systemd/system/sockets.target.wants/ssh.socket".
Removed "/etc/systemd/system/ssh.service.requires/ssh.socket".
root@ip-10-130-177-181:~# sudo systemctl restart ssh
root@ip-10-130-177-181:~# ss -tulpn | grep ssh
tcp   LISTEN 0      128                0.0.0.0:22         0.0.0.0:*    users:(("sshd",pid=13403,fd=3))                          
tcp   LISTEN 0      128                   [::]:22            [::]:*    users:(("sshd",pid=13403,fd=4))                          
root@ip-10-130-177-181:~# ssh -i ~/.ssh/id_ed25519 -p 2222 teste@127.0.0.1
ssh: connect to host 127.0.0.1 port 2222: Connection refused
root@ip-10-130-177-181:~# echo "Port 2222" | sudo tee /etc/ssh/sshd_config.d/port2222.conf
Port 2222
root@ip-10-130-177-181:~# sudo systemctl restart ssh
root@ip-10-130-177-181:~# ss -tulpn | grep ssh
tcp   LISTEN 0      128                0.0.0.0:2222       0.0.0.0:*    users:(("sshd",pid=14158,fd=3))                          
tcp   LISTEN 0      128                   [::]:2222          [::]:*    users:(("sshd",pid=14158,fd=4))                          
root@ip-10-130-177-181:~# ssh -i ~/.ssh/id_ed25519 -p 2222 teste@127.0.0.1
Welcome to Ubuntu 24.04.4 LTS (GNU/Linux 6.17.0-1012-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Thu Jul 23 22:56:59 UTC 2026

  System load:  0.83               Temperature:           -273.1 C
  Usage of /:   82.4% of 28.02GB   Processes:             217
  Memory usage: 53%                Users logged in:       1
  Swap usage:   0%                 IPv4 address for ens5: 10.130.177.181

 * Ubuntu Pro delivers the most comprehensive open source security and
   compliance features.

   https://ubuntu.com/aws/pro

Expanded Security Maintenance for Applications is not enabled.

13 updates can be applied immediately.
11 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable

83 additional security updates can be applied with ESM Apps.
Learn more about enabling ESM Apps service at https://ubuntu.com/esm


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

teste@ip-10-130-177-181:~$ ^C
teste@ip-10-130-177-181:~$ ssh -p 2222 root@127.0.0.1
The authenticity of host '[127.0.0.1]:2222 ([127.0.0.1]:2222)' can't be established.
ED25519 key fingerprint is SHA256:4csBl0jaGBBkWPMH286MQqWRXpaJAjKqLy69ClWEZ60.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[127.0.0.1]:2222' (ED25519) to the list of known hosts.
root@127.0.0.1's password: 
Permission denied, please try again.
root@127.0.0.1's password: 
