
Request
HTTP://admin:password@sub.inlanefreight.com:80/dashboard.php?login=true#status

| Component    | Example           | Description                                                                        |
| ------------ | ----------------- | ---------------------------------------------------------------------------------- |
| Scheme       | HTTP://           | Uetwork scheme, can be FTP or SSH or STEAM                                         |
| User Info    | admin:password    | Ued to authenticate                                                                |
| Subdomain    | sub               | Used to organise various functions of website and find functions                   |
| Host         | inlanefreight.com | Signifies resource Location, can be ip or hostname                                 |
| Path         | /dashboard.php    | Points to resource being accessed.                                                 |
| Query String | ?login=true       | Consists of parameters and values. webpage functions can read this. seperated by & |
| Fragment     | `#status`         | Fragments processed by browser on client side location. ie jump to headers         |
cURL = client for url
