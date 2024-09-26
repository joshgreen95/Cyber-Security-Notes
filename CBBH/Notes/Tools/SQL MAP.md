	Uses *BEUSTQ* principles
- Boolean Based Blind ` AND 1=1`
- Error Based ` AND GTID_SUBSET(@@version, 0)` (Designed to prompt err)
- Union Based `UNION SELECT ...` 
- Stacked Queries `; DROP TABLE` (injecting statement after query)
- Time based ()
- Inline


*PROTIP*
copy as curl command in burp will format it correctly for sqlmap
can load HTTP request from file. with -r
#### Get vs. Post
GET requests paramets in url --url
POST request parameters in the --data tag

-t allows saving to file

--proxy uses a proxy

#### Response Filtering
- --string to filter strings for valid response
- --titles inspects difference in HTML tag <title/>
- --text-only all html tags are removed and then compares based on text recieved
- --dump saves to csv files
- --can enum database with --where (acts like query)
- --dump-all to dump all
- --schema to dump schema
- --search to search for data
- -C column, -T table 

##### ANTI-CSRF Token Bypass
Define data and use --csrf-token to point out where the csrf token is defined in data string 

##### Randomize
use --Randomize to inject a random string of numbers into a query.

--skip-waf  to bypass wireless application firewall

### File Read/Write
> --is-dba to check permissions
> --file-read to read files (Will save to local file )
> --file-write (specify file) --file-dest (specify file write destination)
> --os-shell




