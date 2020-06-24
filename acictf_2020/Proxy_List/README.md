# Proxy List #

**Category:**	Miscellaneous  
**Points:**	100

**Prompt:** 
* We need you to perform geolocation analysis on this list of IPs. We have attributed it to a malicious proxy network. Report back with the prevalent country of origin: [ips.txt](./ips.txt)

**Hints:** 
* The flag is the name of the origin country (case-sensitive) found most frequently in the list
* Offline geolocation IP analysis can be scripted with a python package or two
* These IPs were collected in late 2019, if necessary you may need to use 'historical' geolocation data

**Solution:**
* I signed up for an access token from [IPinfo](https://ipinfo.io/).
* Then I created a python script to iterate throught the IPs and resolve the countries:
```
#!/usr/bin/python3

import ipinfo

access_token = 'REDACTED'
handler = ipinfo.getHandler(access_token)

with open("./ips.txt",'r') as ipFile:
    ip = ipFile.readline().strip()
    while ip:
        details = handler.getDetails(ip)
        print(details.country)        
        ip = ipFile.readline().strip()
```
* sort ipCountries.txt | uniq -c | sort -bgr
* ACI{Brazil}
