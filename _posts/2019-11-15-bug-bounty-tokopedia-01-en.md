---
title: "[Writeup][Bug Bounty] Manipulation of Likes in Product Reviews on Tokopedia [EN]"
layout: post
date: '2019-11-15 04:00:00'
headerImage: false
star: true
tag:
- bugbounty
- tokopedia
- writeup
category: blog
author: fadhilthomas
---

<p align="center">
  <img src="https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia01/img1.jpg">
</p>

## Intro
Before starting, IDOR is Insecure Direct Object Reference, hereinafter referred to as IDOR, is a condition in which users can access an object without passing the access rights check. (OWASP, 2019)

With IDOR, a user can access, change, and delete data. This makes IDOR a very dangerous security hole. Please note, the bug discussed in this writeup has been patched by Tokopedia and screenshots will be censored because of PII.

----
## Affected Endpoint 
https://ws.tokopedia.com/reputationapp/review/api/v1/likedislike

----
## Impact
Manipulation of number of likes in Product Reviews

----
## Steps to Reproduce 
* Log in to your Tokopedia account and open a product review page.
<br/>
<br/>
* Intercept the connection request, click the like a review button.
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia01/img2.jpg)
<br/>
<br/>
* In the intercepted connection request, there are several parameters, such as:
**product_id** is the product id being reviewed,
**shop_id** is a shop id, and
**user_id** is the user id who likes.
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia01/img3.jpg)
<br/>
<br/>
* Forward request, then get a success reply.
<br/>
<br/>
* To try to manipulate the number of likes is by replacing the user id with another user id, without the need for user interaction. Change user_id with another user id and delete some parameters to bypass user authentication.
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia01/img4.jpg)
<br/>
<br/>
* Forward request.
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia01/img5.jpg)
<br/>
<br/>
* The number of likes has increased.
<br/>
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia01/img6.jpg)
<br/>
![alt text](https://github.com/fadhilthomas/fadhilthomas.github.io/raw/master/assets/images/tokopedia01/img7.jpg)

----
## Timeline
* 23 Feb 2019 : Reported to Tokopedia.
* 23 Feb 2019 : Tokopedia received the report.
* 25 Feb 2019 : Tokopedia declared valid with Severity Medium.
* 01 Apr 2019 : The bug has been fixed.
* 22 May 2019 : Tokopedia sent 1.9 million IDR or $135 as reward.

----
## References
1. https://www.owasp.org/index.php/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet
2. https://www.owasp.org/index.php/Testing_for_Insecure_Direct_Object_References_(OTG-AUTHZ-004)
3. https://www.bugcrowd.com/how-to-find-idor-insecuredirect-object-reference-vulnerabilities-for-large-bountyrewards/