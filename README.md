# Online Book Store

The Book Store Management System is a web-based application that allows users to browse, search, and purchase books online. It provides a user-friendly interface for customers to explore the bookstore's catalog, add items to their cart, and complete transactions securely. Additionally, the system offers administrative capabilities for managing inventory, processing orders, and monitoring sales

## Features

### User Features:
- Browse books as a list
- Search for specific books
- View book details like authors, published date and price
- Add books to cart and checkout

### Admin Features:
- Add, update, or remove books
- Show and search in inventory of books
- View order history and bills

## Project Structure

- *Backend:* Spring Boot, Java, Hibernate (JPA)
- *Frontend:* Thymeleaf templates (HTML), CSS, JavaScript
- *Database:* MySQL (configurable via application.properties)
- *Build Tool:* Maven
- *Testing:* JUnit, Mockito for unit tests
- *Containerization:* Docker (Dockerfile included)

## Installation

### Prerequisites

- JDK 11 or later
- Maven 3.6+
- MySQL 8.0+ (or any SQL-compatible DB)
- Docker (optional)

### Steps to Run:

1. Download this program file on your computer
```bash
https://projectworlds.in/wp-content/uploads/2024/03/bookstore.zip
```
2. Run the source code on Eclipse.
3. Go to any browser on your device
4. Write this URL 127.0.0.1:8080

## code implementation


### 1.**AddBook**
The Add Book feature allows users to create and submit a new book entry into the bookstore's inventory. This functionality is accessible via the Add Book form, which prompts users to input essential details for the new book.
```java
@GetMapping("/add")
	public String addBook(Model model) {
		model.addAttribute("book", new Book());
		return "form";
	}
```
### Example API Body for Adding a Book
```json
{
  "title": "Effective Java",
  "author": "Joshua Bloch",
  "publishedDate": "2018-01-06",
  "isbn": "9780134685991",
  "price": 45.99,
  "description": "A comprehensive guide to best practices in Java programming.",
  "category": "Programming",
  "stock": 100
}
```
### Explanation of Fields
`title`: The title of the book.<br>
`author`: The author(s) of the book.<br>
`publishedDate`: The date when the book was published, typically in ISO format (YYYY-MM-DD).<br>
`isbn`: A unique identifier for the book, often used as a standard identifier for books.<br>
`price`: The price of the book.<br>
`description`: A short description of the book’s contents.<br>
`category`: The genre or category of the book, such as "Programming" or "Fiction."<br>
`stock`: The number of copies available in the bookstore.<br>

### 2.**DeleteBook**
The Delete Book feature allows users to remove a book from the bookstore's inventory. This functionality is essential for maintaining an up-to-date collection by enabling users to delete any books that are no longer available or necessary.
```java
 public String deleteBook(@PathVariable Long id, RedirectAttributes redirect) {
		bookService.delete(id);
		redirect.addFlashAttribute("successMessage", "Deleted book successfully!");
		return "redirect:/book";
	}
```
### Delete Book API Body<br>
For the Delete operation, typically no body is sent; only the book ID is needed in the URL:<br>

**Endpoint**: DELETE /book/{id}<br>
**Example URL**: DELETE /book/1<br>
*No request body is needed, as the book ID is passed in the URL*.<br>

### 3.**PlaceOrder**
The Place Order feature enables customers to finalize their purchase by placing an order for items in their shopping cart. This functionality is crucial for completing transactions and ensuring customers receive their ordered products.

```java
public String placeOrder(@Valid Customer customer, BindingResult result, RedirectAttributes redirect) {
		if (result.hasErrors()) {
			return "/checkout";
		}
		billingService.createOrder(customer, shoppingCartService.getCart());
		//emailService.sendEmail(customer.getEmail(), "bookstore - Order Confirmation", "Your order has been confirmed.");
		shoppingCartService.emptyCart();
		redirect.addFlashAttribute("successMessage", "The order is confirmed, check your email.");
		return "redirect:/cart";
	}
```
### Place Order API Body

```json
{
  "customer": {
    "name": "Jane Doe",
    "email": "jane.doe@example.com",
    "address": "123 Main St, Anytown, USA",
    "phone": "555-1234"
  },
  "items": [
    {
  "bookId": 1,
      "quantity": 1
    },
    {
      "bookId": 2,
      "quantity": 2
    }
  ]
}
```
### Field Explanation<br>
`customer`: An object containing customer information.<br>
`name`: The full name of the customer.<br>
`email`: The customer's email address.<br>
`address`: The shipping address where the order should be sent.<br>
`phone`: The contact phone number of the customer.<br>
`items`: An array of items being ordered.<br>
`bookId`: The unique identifier for the book being ordered.<br>
`quantity`: The number of copies of that book to order.<br>

### 4.**Checkout**
The Checkout feature allows customers to review their shopping cart and input their information before placing an order. This functionality is vital for finalizing purchases and ensuring a smooth transition from shopping to ordering.

```java
public String checkout(Model model) {
		List<Book> cart = shoppingCartService.getCart();
		if (cart.isEmpty()) {
			return "redirect:/cart";
		}
		model.addAttribute("customer", new Customer());
		model.addAttribute("productsInCart", cart);
		model.addAttribute("totalPrice", shoppingCartService.totalPrice().toString());
		model.addAttribute("shippingCosts", shoppingCartService.getshippingCosts());
		return "checkout";
	}
```

### Checkout API Body<br>
To checkout, use the following request body:

```json
{
  "customer": {
    "name": "Jane Doe",
    "email": "jane.doe@example.com",
    "address": "123 Main St, Anytown, USA",
    "phone": "555-1234"
  },
  "cart": [
    {
      "bookId": 1,
      "quantity": 1
    },
    {
      "bookId": 3,
      "quantity": 1
    }
  ],
  "totalPrice": 101.98,
  "shippingCosts": 5.00
}
```
### Field Explanation<br>
`customer`: An object with the same fields as in the Place Order API.<br>

`cart`: An array representing items in the shopping cart.<br>

`bookId`: The unique identifier for each book in the cart.<br>
`quantity`: The number of copies for each book in the cart.<br>
`totalPrice`: The total price of all items in the cart.<br>

`shippingCosts`: The cost associated with shipping the order.<br>




### 5.**AddToCart**
This method adds a specific book to the user's shopping cart based on the book's unique ID.

```java
public String addToCart(@PathVariable("id") Long id, RedirectAttributes redirect) {
		List<Book> cart = shoppingCartService.getCart();
		Book book = bookService.findBookById(id).get();
		if (book != null) {
			cart.add(book);
		}
		shoppingCartService.getSession().setAttribute("cart", cart);
		redirect.addFlashAttribute("successMessage", "Added book successfully!");
		return "redirect:/cart";
	}
```

Add to Cart API Body<br>
To add a book to the cart, use the following request body:

```json

{
  "bookId": 1,
  "quantity": 1
}
```
### Field Explanation<br>
`bookId`: The unique identifier for the book to add to the cart.<br>
`quantity`: The number of copies of the book to add.<br>


### 6.**RemoveFromCart**
This method removes a specific book from the user's shopping cart based on the book's unique ID.

```java
@GetMapping("/remove/{id}")
	public String removeFromCart(@PathVariable("id") Long id, RedirectAttributes redirect) {
		Book book = bookService.findBookById(id).get();
		if (book != null) {
			shoppingCartService.deleteProductWithId(id);
		}
		redirect.addFlashAttribute("successMessage", "Removed book successfully!");
		return "redirect:/cart";
	}
```
### Remove from Cart API Body<br>
To remove a specific book from the cart, use the following endpoint:<br>

Endpoint: DELETE /cart/remove/{id}<br>
### Field Explanation<br>
`id`: The unique identifier for the book to be removed from the cart, specified in the URL. No body is required.


### 7.**RemoveAllFromCart**
 This method clears all items from the user's shopping cart.

 ```java
@GetMapping("/remove/all")
	public String removeAllFromCart() {
		List<Book> cart = shoppingCartService.getCart();
		cart.removeAll(cart);
		return "redirect:/cart";
	}
```
### Remove All from Cart API Body
To clear all items from the cart, use the following endpoint:<br>

Endpoint: DELETE /cart/remove/all<br>
### Field Explanation
*This operation does not require any body. It simply clears all items from the cart when the endpoint is called*.



# GNU General Public License

This program is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation; either version 3 of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

The Online Book Store is protected under the terms of the GNU General Public License 
Project By: Yugesh Verma
Company Name : Projectworlds LLP
Email Address : official@projectworlds.in
Phone Number : +916263056779
For more information, visit the project’s official https://projectworlds.in/java-projects-with-source-code

