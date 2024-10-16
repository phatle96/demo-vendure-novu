## E-commerce Demo App

### How to use?

#### I. Set-up:

1. In ./vendure-app just run the command

```sh
docker compose up
```

2. It will create these docker containers:
	1. Vendure server
		1. Shop API server: http://localhost:3000/shop-api 
		2. Admin API server: http://localhost:3000/admin-api 
		3. Assets server: http://localhost:3000/assets 
		4. Dev Mailbox server: http://localhost:3000/mailbox 
		5. Admin UI: http://localhost:3000/admin (**Back-office app**)
	2. Vendure worker (to index database, queue, ... )
	3. Notification server
		1. Novu Bridge Server: http://localhost:4000/api/novu
	4. Vendure store-front app: http://localhost:4200
	5. Postgres Database expose at port :5433

3. At this point, if you do not run into any error, the app can function normally, but no notification feature. We need to take further steps to get the notification feature work
   
   - We take advantage of the Novu Cloud, so we no need to implement all of services that a notification server required (database, worker, queue, storage,...), just define a business logic (when/ why/ how to send notifications to the users) on our side and sync to the Novu cloud, the app we implement is called **Bridge endpoint** in the Novu concept (https://docs.novu.co/concepts/endpoint)
   - Because the Novu Cloud workers need to communicate with our Bridge Endpoint, so we need to public it on network, I suggest to use the Cloudflare Tunnel
   
	1. Install cloudflared on your host machine 
	      
		```sh
		brew install cloudflared  # for macOS
		```
	  
	 2. Authenticate with Cloudflare
	    
		```sh
		cloudflared tunnel login
		```
	
	3. Run Cloudflare Tunnel
	   
		```sh
		cloudflared tunnel --url http://localhost:4200
		```

	4. It will return a Tunnel URL (for example: https://standing-aged-efficiency-lite.trycloudflare.com). Then we will connect our bridge endpoint to the Novu Cloud, don't forget to add **/api/novu** suffix to the end of the tunnel URL. Run this script, notice that you need nodejs installed on your machine to use npx command
	   
		```sh
		npx novu@latest sync --bridge-url  https://standing-aged-efficiency-lite.trycloudflare.com/api/novu --secret-key 5d8e8966bc3060ab827482ed2e4a78f0
		```
	   
	   If no error return but nothing, it's work!!!

#### II. Test the app:

1. The app is populated with the sample data, you could go to the store-front app (http://localhost:4200) to view as a customer role
2. Currently, the customer can not create the account yet, use the admin ui (http://localhost:3000/admin) to create. 
   You need an admin account to access the back-office, use this account (id: superadmin/ password: superadmin)
3. In the Admin UI, customers page, use could create some customer accounts (http://localhost:3000/admin/customer/customers)
4. Use those accounts to login to the store-front app, use need to reload the page to take effect (it's a feature)
5. Now, you can test the notification feature which will pop the message up on the store-front app when the admin sent, just select users then hit the send nofification button and wait for result
![img 1](./doc-assets/Pasted%20image%2020241016182329.png)