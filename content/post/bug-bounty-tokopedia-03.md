---
title: "[Writeup][Bug Bounty][Tokopedia] Manipulate Other User’s Cart and Wishlist on Tokopedia [EN]"
date: '2020-07-03 04:00:00'
tags: ['bugbounty','tokopedia','writeup']
categories: ['post']
author: "fadhilthomas"
draft: false
---

![alt text](/tokopedia01/img1.jpg)

Before starting, IDOR is Insecure Direct Object Reference, hereinafter referred to as IDOR, is a condition in which users can access an object without passing the access rights check. (OWASP, 2019)
>*With IDOR, a user can access, change, and delete data. This makes IDOR a very dangerous security hole. Please note, the bug discussed in this writeup has been patched by Tokopedia, and screenshots will be censored because of PII.*

----
## Affected Endpoint
```
https://[redacted]/cart/v2/shop_group
```
```
https://[redacted]/cart/v2/add_product_cart
```
```
https://[redacted]/cart/v2/update_cart
```
```
https://[redacted]/cart/v2/remove_product_cart
```
----
## Impact
Several things can be done by using these vulnerabilities:

* Users can add a product to another user's wishlist.
* Users can view the product list in the shopping cart of other users.
* Users can add a product to another user's shopping cart.
* Users can change the number of products in another user's shopping cart.
* The user can delete the product list in another user's shopping cart.

----
## Steps to Reproduce
>* I set up two accounts, Account A as Attacker with ID: 37822XXX and Account B as Victim with ID: 49468XXX.
>* Because this finding is chaining which means it involves more than one endpoint, I will divide this article into several sections.

#### Part 1. Viewing Products in Other Users' Cart.
* Login to Attacker's Account.
* Navigate to open the shopping cart to get requests like in Figure 1.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-1.png)
*Figure 1. Viewing Shopping Cart Request*

* To view products from the Victim's Account shopping cart, then in the request body, change the X-User-ID and Tkpd-UserId, which were initially Account ID A to Account ID B, as shown in Figure 2.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-2.png)
*Figure 2. Changed Request*
* See the response of requests that have been changed. The request was successful and got product list information in Victim's Account shopping cart such as cart_id, and product list information such as product_id, product quantity, total price, and many more as in Figures 3 and 4.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-3.png)
*Figure 3. Response From Changed Requests*
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-4.png)
*Figure 4. Response From Changed Requests*
* Save cart_id information to use in the process of changing and deleting the shopping cart and adding wishlist products that will later require cart_id.

#### Part 2. Adding Products to Other Users' Shopping Cart.
* Login to Attacker's Account.
* Add a product to the shopping cart to get requests like in Figure 5.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-5.png)
*Figure 5. Add Product to Shopping Cart Request*
* To perform the action of adding products to the Victim's Account shopping cart, then in the request body, change the X-User-ID and Tkpd-UserId which were originally Account ID A to Account ID B as shown in Figure 6.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-6.png)
*Figure 6. Changed Request*
* See the response of requests that have been changed. The request was successful and got responses such as cart_id, shop_id and customer_id as in Figure 7.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-7.png)
*Figure 7. Response From Requests That Have Been Changed*
* Then for the verification process, log in to Victim's Account, then select the shopping cart. The result is that the product has been successfully added to Victim's Account as shown in Figure 8.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-8.png)
*Figure 8. Adding Products Successfully*

#### Part 3. Changing Products in Other Users' Shopping Cart.
* Login to Attacker's Account.
* Changes the number of products in the shopping cart and get request like in Figure 9.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-9.png)
*Figure 9. Change Product in Shopping Cart Request*
* To change the quantity of products to the Victim's Account shopping cart, then in the request body, change the X-User-ID, Tkpd-UserId and user_id which were originally Account ID A with Account ID B and change cart_id with cart_id obtained from the process of adding the previous product and also change the quantity desired as shown in Figure 10.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-10.png)
*Figure 10. Changed Request*
* See the response of requests that have been changed. The request was successful and got a response such as ok status as shown in Figure 11.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-11.png)
*Figure 11. Response from Changed Requests*
* Then for the verification process, log in to Victim's Account, then select the shopping cart. The result is the quantity of products successfully changed in the Victim's Account shopping cart as shown in Figure 12.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-12.png)
*Figure 12. Changing Products in the Shopping Cart Successfully*

#### Part 4. Delete Other Users' Shopping Cart.
* Login to Attacker's Account.
* Try to remove all products in the shopping cart to get requests that are later needed for the attack process like in Figure 13.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-13.png)
*Figure 13. Delete Shopping Cart Request*
* To delete all products in the Victim's Account shopping cart, then in the request body, change the X-User-ID, Tkpd-UserId and user_id which were originally Account ID A with Account ID B and also change cart_id with cart_id obtained during the process add the product to the shopping cart as shown in Figure 14.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-14.png)
*Figure 14. Changed Request*
* See the response of requests that have been changed. The request was successful and got a response ok status.
* Then for the verification process, log in to Victim's Account, then select the shopping cart. The result is that all products are successfully removed from the Victim's Account shopping cart as shown in Figure 15.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-15.png)
*Figure 15. Removing Shopping Cart Successfully*

#### Part 5. Adding Products to Other Users' Wishlist.
* The process of adding products to the Wishlist Victim's Account list is a continuation of the process of removing products from the shopping cart. When making a request to remove a product from the shopping cart there is an "add_wishlist" parameter: 0.
* So to add products to the Wishlist Victim's Account list, change the add_wishlist parameter from 0 to 1 as shown in Figure 16.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-16.png)
*Figure 16. Changed Request*
* See the response of requests that have been changed. The request was successful and got a response such as ok status as shown in Figure 17.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-17.png)
*Figure 17. Response From Requests That Have Been Changed*
* Then for the verification process, log in to Victim's Account, then select Wishlist. The result is that the product was successfully added to Wishlist Victim's Account as shown in Figure 18.
![](https://blog.sumatracysec.id/wp-content/uploads/2020/05/image-18.png)
*Figure 18. Adding Products to Wishlist Successfully*

----
## Remediation
* Apply access control.
* Users need to be authorized for information requested before the server provides it.

----
## Timeline
* 17 Apr 2019 : Reported to Tokopedia.
* 17 Apr 2019 : Tokopedia received the report.
* 18 Apr 2019 : Tokopedia declared valid with Severity Medium.
* 29 Aug 2019 : The bug has been fixed.
* 20 Nov 2019 : Tokopedia sent 1.9 million IDR or $135 as reward.

----
## References
1. https://www.owasp.org/index.php/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet
2. https://www.owasp.org/index.php/Testing_for_Insecure_Direct_Object_References_(OTG-AUTHZ-004)
3. https://www.bugcrowd.com/how-to-find-idor-insecuredirect-object-reference-vulnerabilities-for-large-bountyrewards/