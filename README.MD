# **E-commerce Demo App**

## **I. Overview:**
---
The demo is deployed on http://vendure-dev.innity.com.my that are included:

###  **1. Vendure App**

#### 1.1 Back-end:
1. [Shop API server](http://vendure-dev.innity.com.my:3000/shop-api) 
2. [Admin API server](http://vendure-dev.innity.com.my:3000/admin-api )
3. [Assets server](http://vendure-dev.innity.com.my:3000/assets )
4. [Dev Mailbox server](http://vendure-dev.innity.com.my:3000/mailbox) 
5. Vendure worker server (to index database, queue, ... )
6. Postgres Database
#### 1.2 Client:
1. [Admin UI](http://vendure-dev.innity.com.my:3000/admin) (**Back-office app**)
	 - Default admin account: 
		- Username: superadmin
		- Password: superadmin
2. [Vendure store-front](http://vendure-dev.innity.com.my/)

### **2. Pinkary App**
1. [Pinkary Laravel app](http://vendure-dev.innity.com.my:8000)

### **3. Notification server:** 
1. [Novu bridge app](http://vendure-dev.innity.com.my:4000)

### **4. Telegram Bot**
1. [Vendure Mail Bot](http://vendure-dev.innity.com.my:3000)


## **II. TEST THE APP:**
---
###  Use case 1:  Send notifications from Vendure admin to store-front app user

1. The app is populated with the sample data, you could go to [the store-front app](http://vendure-dev.innity.com.my:4200) to view as a customer role
2. Currently, the customer can not create the account yet, use [the admin UI](http://vendure-dev.innity.com.my:3000/admin)  to create.  
   You need an admin account to access the back office, use this account (id: superadmin/ password: superadmin)
3. In [the Admin UI, customers page](http://vendure-dev.innity.com.my:3000/admin/customer/customers), you can create some customer accounts 
4. Use those accounts to log in to the store-front app, you need to reload the page to take effect (it's a feature)
5. Now, you can test the notification feature which will pop the message up on the store-front app when the admin sends, just select users then hit the send notification button -> write message -> send, then wait for the result

![img](./doc-assets/Pasted%20image%2020241016182329.png)


### Use case 2: Broadcast notification to Store-front app users whenever Pinkary user creates a new post:

1. Go to [Pinkary app](http://vendure-dev.innity.com.my:8000) and create a new user account
2. The app will ask to verify your email, but you can create the post without email verifying 
	1. In case you want to use full app features, you must verify your account email. 
	2. Because we have not implemented any email provider for our demo app yet, so we will use Telegram Bot as an alternative way to get email from Pinkary. I created [@VenderMailBot](https://t.me/venduremailbot) as a mailbox to handle the email verification job
	   ![IMG_0193.png](./doc-assets/IMG_0193.PNG)
	3. Just access the Telegram app on your phone or web, find the @VenderMailBot bot, then **/start** the chat, and send it the email address that you use to register the Pinkary account
	   By entering your Pinkary register email to the bot, you will get the latest verifying email from Pinkary, and also subscribe to receive any emails that are sent from Pinkary in the future. 
3. Create a new post from Pinkary, and then the notification will be broadcast to all of the [store-front app](http://vendure-dev.innity.com.my/) users, you will see the notification popup on the store-front app (user login required) with its content the same as the Pinkary post

