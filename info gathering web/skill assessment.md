## — What is the IANA ID of the registrar of the inlanefreight.com domain?
```
whois inlanefreight.com
```
- 468

### What http server software is powering the inlanefreight.htb site on the target system? Respond with the name of the software, not the version, e.g., Apache.
```
 curl -i inlanefreight.htb:30552
```
- nginx

### What is the API key in the hidden admin directory that you have discovered on the target system?
```
gobuster vhost -u http://inlanefreight.htb:30552 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -t 100 --append-domain 
// found web1337.inlanefreight.htb
curl -i web1337.inlanefreight.htb:30552/robots.txt
curl -i web1337.inlanefreight.htb:30552/admin_h1dd3n/
```
- e963d863ee0e82ba7080fbf558ca0d3f



### After crawling the inlanefreight.htb domain on the target system, what is the email address you have found? Respond with the full email, e.g., mail@inlanefreight.htb.
```
gobuster vhost -u http://web1337.inlanefreight.htb:30552 -w /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt -t 500 --append-domain
python3 ReconSpider.py http://dev.web1337.inlanefreight.htb:30552
cat results.json
```

- 1337testing@inlanefreight.htb

## 5 — What is the API key the `inlanefreight.htb` developers will be changing to?
- ba988b835be4aa97d068941dc852ff33