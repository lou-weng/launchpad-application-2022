# launchpad-application-2022

## Premise:
You are a business owner at Launch-Pizza. One day you decide to open your store to online purchases. The basic functionality must allow any customer to order a pizza from a set menu, track the order status of the pizza, and be able to see a receipt for their order for up to one year. Describe and give examples of a basic API and its endpoints that will accomplish this task. You do not need to extensively document this API.

## Assumptions
* Pizzas will be offered in standardized sizes [Small, Medium, Large]
* Only pizzas are offered for online purchase, not other items (this only effects some of the naming of objects I have in this write-up)
* Assuming that the online purchases will be created on a new web-application and not on a platform like Shopify 
* For an MVP of this pizza shop service, certain elements will be assumed to already exist:
  * Authentication and user accounts: 
    * Can be implemented quickly using services like **Firebase** or **AWS Cognito**
    * Necessary to prevent abuse of ordering system (prank orders) and for users to track their own order history
  * Payment API. Easiest solution would be to use an external vendor who provides APIs for financial transactions like **Stripe**

## Ideal User Journey
We will focus on some implementation details of the above requirements:
  1. allow users to purchase a pizza by making an order
  2. allow users to review their orders
> We are going to focus on the customer experience and leave out implementation details from the business side (other APIs needed to update order status)

#### USER: Purchasing a pizza
1. User visits web-application for pizza shop
	- user may need to login or set up their profile before an order (out of scope)
	- **API needed to authenticate/log users in/create accounts (out of scope)**
2. User views the set menu for the pizza shop. 
	-  user will want to see list of pizzas, their ingredients, and price 
	-  **API needed to retrieve list of pizzas**
3. When user sees a pizza they want to order, they will make a selection on a web form to select which pizza they want to buy. 
	- user will be able to select the type of pizza
	- user will be able to select the quantity of selected pizza and size
4. When user is ready to purchase, they will go to a checkout page
	- user will need to confirm their order details
	- user will need to enter basic identification details
5. When user is ready to finalize purchase, they will click on a button to confirm their pizza order
	- **API needed to persist order to database**
	- **API needed to process financial transaction (out of scope)**

#### USER: Reviewing an order
1. User is able to see a list of past orders (assuming on some orders page)
	- **API to retrieve all past orders from a specific user (based on user ID)**
2. User is able to see details of a single order (assuming user clicks on a specific order)
	- **API to retrieve a specific order (based on order ID)**

##### Pizza Object
```
	{
		id: String (PK)
		ingredients: String[]
		price: int 			// price given in cents
	}
```

##### OrderItem Object
```
	{
		item: String 		// pizza ID
		size: String
		quantity: int
	}
```

##### Order Object
```
	{
		id: String (PK)
		user_id: String
		total_price: int
		order_status: String
		order_date: String
		items: OrderItem[]		// list of pizza orders
	}
```

### Data Rationale
I've opted for a NOSQL schema because of the way data is structured here. Each order may have an arbitrary number of pizzas being ordered. When orders are retrieved, the data shown to users are all related to a specific order object in a single table. 

Retrieving a single order object from a NOSQL store will be faster and more scalable then having to make joins from an order and order-item table. 

## API
| Use case | Endpoint | Type | Details | 
| --- | --- | --- | --- |
| View set pizza menu | /menu | GET | Retrieves a list of pizzas
| Create an order | /create-order | POST | Send order object in HTTP request body
| View orders by user | /orders | GET | Provide user id to get orders by user
| View order | /orders | GET | Provide order id to get order details

## Domain Model 
* Key entities involved with the API:
	- pizza (type, ingredients, price)
	- order (contains quantity of pizza, total price, order status)
	- receipt (contains order, payment type)
	- user (out of scope)