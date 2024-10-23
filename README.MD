## E-commerce Demo App

### How to use?

#### I. SET-UP:

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


![img](./doc-assets/Pasted%20image%2020241016183147.png)

3. At this point, if you did not run into any error, the app can function normally. Woo hoo!

#### UPDATE (Otc 23, 2024)

1. In previous version, I forgot to include .env file of the Novu service, so that it not run correctly. In this version, I tested on my VPS, it works, let's try this setup
   
   We need to change the HOST_URL and NOVU_BRIDGE_URL corresponding to your host name/ ip

```sh
  #novu-notification/my-novu-app/.env
  
  HOST_URL=${YOUR_HOST_NAME}
  NOVU_SECRET_KEY=${NOVU_API_KEY}
  NOVU_BRIDGE_URL=${YOUR_HOST_NAME}/api/novu
```
		
2. You must change the URI of the additional API in this file , currently I don't know how to use env file in Vendure plugin, so we need to hard-code it by hand at this time!
  
	```ts
	//my-special-shop/src/plugins/custom-ui-plugin/ui/ui-service/admin-notification.service.ts
	...
	
	client = new ApolloClient({
		link: new HttpLink({
			uri: `http://${YOUR_HOST_NAME}:3000/admin-api`,
		}),
		cache: new InMemoryCache(),
	});
	
	...
	```
	
3. Change apiHost in environment file of Store-front app
   
   ```ts
   //storefront-angular-starter/src/environments/environment.ts
   
	export const environment = {
		production: false,
		apiHost: `${YOUR_HOST_NAME}`,
		apiPort: 3000,
		shopApiPath: 'shop-api',
		baseHref: '/',
		tokenMethod: 'bearer',
		novuApplicationIdentifier: `${NOVU_APP_IDENTIFIER}`
	};
   	```

4. Change URL in .graphqlconfig file
   
   ```json
   //storefront-angular-starter/.graphqlconfig

	{
    "schemaPath": "./schema.graphql",
    "extensions": {
        "endpoints": {
            "demo": "https://demo.vendure.io/shop-api",
            "dev": `${YOUR_HOST_NAME}/shop-api`
	        }
	    }
	}
	```
	
### II. USE WITH CAUTION

At the time I write this demo, the app works as expected, but only for demo and testing purposes, it omits all of the security issues and can cause data leakage. you need to notice about these things:

1. API key: 
	- I put my Novu API key in .env file, and in few lines of code in Vendure service where I struggle to make the env variable work but can't. 
	- You need to change the Novu public API key in store-front app too, I currently use my own key, in Novu concept it called Application Identifier, I use it to connect the user to the Novu socket server (use could find the key in the Angular environment file)

2. Client app: I currently run the client app when starting docker (for testing purposes), you need a NGINX proxy to let the users get the built files and run it on their browser

3. Notification service: 
   We take advantage of the Novu Cloud, so we no need to implement all of services that a notification server required (database, worker, queue, storage,...), just define a business logic (when/ why/ how to send notifications to the users) on our side and sync to the Novu cloud, the app we implement is called **Bridge endpoint** in the Novu concept (https://docs.novu.co/concepts/endpoint)	
   
	Because the Novu Cloud workers need to communicate with our Bridge Endpoint (https://docs.novu.co/deployment/production), so we need to public it on network
   

   If you deploy it locally (on your machine, not a VPS or other server), you need a thing called tunnel to expose the app on network, currently uses Cloudflare Tunnel. When you start the Notification server, it will run the script which do these things
	- Start the Novu bridge Endpoint Service
	- Run the Cloudflare tunnel to get the tunnel URL. Currently, I make use of Cloudflare with no authentication, you need a Cloudflare API key for the tunnel not to suddenly shut down 
	- Sync our bridge endpoint with Novu server. You must talk to the Novu Cloud that your app is running on this endpoint and Novu Cloud will take care of your business

   
#### III. TEST THE APP:

1. The app is populated with the sample data, you could go to the store-front app (http://localhost:4200) to view as a customer role
2. Currently, the customer can not create the account yet, use the admin ui (http://localhost:3000/admin) to create. 
   You need an admin account to access the back-office, use this account (id: superadmin/ password: superadmin)
3. In the Admin UI, customers page, use could create some customer accounts (http://localhost:3000/admin/customer/customers)
4. Use those accounts to login to the store-front app, use need to reload the page to take effect (it's a feature)
5. Now, you can test the notification feature which will pop the message up on the store-front app when the admin sent, just select users then hit the send nofification button -> write message -> send, then wait for the result


![img](./doc-assets/Pasted%20image%2020241016182329.png)
