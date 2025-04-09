***chmod 600 for ssh keys and login with -i for id files*** 
```shell
chmod 600 
ssh ip -i
```

***When using smtp-user-enum  USE ALL 3***
`VRFY` `RCPT` `EXPN`

***Remember NMAP can Scan UDP***
```
nmap -sU IP
```

#### Find Writeable files
```shell-session
find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null
```