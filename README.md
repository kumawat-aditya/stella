# Stella | A Shopping Website ( Backend )

## Project Overview

This project is a secure e-commerce shopping website backend developed using Java Spring Boot. It includes user authentication, product management, review and ratings, order management, and payment gateway integration to provide a complete online shopping experience.

## Features

- **User Authentication:** Secure user registration and login using Spring Security and JWT Authentication.
- **Product Management:** RESTful APIs to manage product listings, including adding, updating, and deleting products.
- **Order Management:** RESTful APIs to handle orders, including creating, viewing, and managing orders.
- **Payment Gateway Integration:** Seamless and secure payment processing for orders.

## Technologies Used

- **Backend Framework:** Spring Boot
- **Security:** Spring Security, JWT
- **Database:** MySQL/MariaDB
- **Payment Gateway:** Integration with a popular payment gateway (e.g., Stripe, PayPal)
- **Tools:** Visual Studio Code, Eclipse, Git, GitHub
- **Languages:** Java, SQL

## Getting Started

### Prerequisites

- Java 8 or higher
- Maven
- MySQL or MariaDB
- IDE (e.g., Visual Studio Code, Eclipse)

### Installation

1. **Clone the repository:**

   ```sh
   git clone https://github.com/kumawat-aditya/stella.git
   cd stella
   ```

2. **Set up the database:**
   - Create a database named `stella_db` in MySQL/MariaDB.
   - Update the database configuration in `src/main/resources/application.properties` with your database credentials.

3. **Build the project:**

   ```sh
   mvn clean install
   ```

4. **Run the application:**
   ```sh
   mvn spring-boot:run
   ```

### API Endpoints

#### User Management

- **Register User:** `POST /api/users/register`
- **Registration Verification:** `POST /api/users/verify-user?token=token`
- **Resend Registration Verification:** `POST /api/users/register/resend-token`
- **Login User:** `POST /api/auth/login`
- **Fetch User Details:** `GET /api/users`
- **Update User Name:** `PUT /api/users/name`
- **Update User Number:** `PUT /api/users/number`
- **Update User Email:** `PUT /api/users/email`
- **Update User Password:** `PUT /api/users/password`
- **Verify Update Details:** `POST /api/users/verify-update?token=token`
- **Deactivate User Account:** `PUT /api/users/de-activate`
- **Save User Address:** `POST /api/users/address`
- **Fetch User Addresses:** `GET /api/users/address`
- **Update User Address:** `PUT /api/users/address`
- **Delete User Address:** `DELETE /api/users/address?id=ID`

#### Seller Management

- **Seller Registration:** `POST /api/sellers/register`
- **Upgrade User to Seller:** `POST /api/sellers/upgrade`
- **Update Store Name:** `PUT /api/sellers/store-name`
- **Update Seller Address:** `PUT /api/sellers/address`
- **Set Store Logo:** `POST /api/sellers/logo`
- **Update Store Logo:** `PUT /api/sellers/logo`
- **Verify Update:** `POST /api/sellers/verify-update?token=TOKEN`

### Seller Dashboard

- **Fetch Orders for Seller:** `GET /api/sellers/dashboard`
- **Update Order Status for Seller:** `POST /api/sellers/dashboard/update-order-status`
- **Fetch Products for Seller:** `GET /api/sellers/dashboard/products`

#### Product Management

- **Create Product:** `POST /api/products`
- **Update Product:** `PUT /api/products`
- **Search Products by Store Name:** `GET /api/products/store?store=STORE-NAME`
- **Search Product by Name:** `GET /api/products/search?search=PRODUCT-NAME`
- **Deactivate Product:** `DELETE /api/products/de-activate?id=ID`
- **Activate Product:** `POST /api/products/activate?id=ID`

#### Review And Rating Management

- **Create Review:** `POST /api/review-rating`
- **Update Review:** `PUT /api/review-rating/ID`
- **Fetch Reviews By Product ID:** `GET /api/products/review-rating`
- **Fetch Reviews For User:** `GET /api/users/review-rating`
- **Get Averate Product Rating:** `GET /api/products/average-rating`

#### Order Management

- **Create Order:** `POST /api/orders`
- **Create Order By Cart:** `POST /api/orders/order-by-cart`
- **Order Payment Callback:** `POST /api/orders/payment-callback`
- **Fetch User Orders:** `GET /api/orders`
- **Track Order:** `GET /api/orders/track?order_id=ID`

#### Cart Management

- **Crate Cart / Add to Cart:** `POST /api/users/cart`
- **Update Cart:** `PUT /api/users/cart`
- **Fetch Carts:** `GET /api/users/cart`
- **Delete Cart:** `DELETE /api/users/cart`

## Usage

- **Swagger UI:** Access the API documentation at `http://localhost:8080/swagger-ui.html` after running the application.
- **Admin Panel:** A simple admin panel for managing products can be added for convenience.

## Contributing

Contributions are welcome! Please fork the repository and submit a pull request for any feature additions, bug fixes, or improvements.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Contact

**Aditya Kumawat**  
Email: [kumawataditya105@gmail.com](mailto:kumawataditya105@gmail.com)  
LinkedIn: [Aditya Kumawat](http://www.linkedin.com/in/adityakumawat105)  
GitHub: [kumawat-aditya](http://github.com/kumawat-aditya)
