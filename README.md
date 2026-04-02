# Stella — E-Commerce Backend API

Stella is a full-featured, RESTful e-commerce backend built with **Spring Boot 3**. It provides a complete platform for buyers and sellers: user registration with email verification, JWT-based stateless authentication, product management with multi-image file uploads, a multi-cart shopping system, Razorpay-integrated order and payment processing, seller dashboards with order status management, and a product review and rating system.

---

## Tech Stack

| Layer     | Technology                                         |
| --------- | -------------------------------------------------- |
| Language  | Java 17                                            |
| Framework | Spring Boot 3.3.1                                  |
| Security  | Spring Security 6 + JWT (jjwt 0.12.5)              |
| ORM       | Spring Data JPA (Hibernate)                        |
| Database  | MariaDB                                            |
| Payments  | Razorpay Java SDK 1.4.6                            |
| Email     | Spring Mail (SMTP / Gmail)                         |
| File I/O  | Apache Commons IO 2.16.1                           |
| Build     | Maven (spring-boot-maven-plugin)                   |
| Utilities | Lombok, Spring Boot Actuator, Spring Boot DevTools |

---

## Prerequisites

- **Java 17** or higher
- **Maven 3.8+**
- **MariaDB** (or compatible MySQL) running locally on port `3306`
- A **Gmail account** with an App Password configured for outbound email
- A **Razorpay** test account for payment processing

---

## Installation & Running Locally

### 1. Clone the repository

```bash
git clone https://github.com/kumawat-aditya/stella.git
cd stella
```

### 2. Create the database

```sql
CREATE DATABASE ecommerce;
CREATE USER 'ecommerce'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON ecommerce.* TO 'ecommerce'@'localhost';
FLUSH PRIVILEGES;
```

### 3. Configure application properties

Edit `src/main/resources/application.properties` with your credentials. See the [Environment Variables](#environment-variables) table below for all required keys.

### 4. Build and run

```bash
./mvnw spring-boot:run
```

The API will be available at `http://localhost:8080`.

---

## Environment Variables

All configuration lives in `src/main/resources/application.properties`. **Never commit real credentials.**

| Key                                                | Description                                                            |
| -------------------------------------------------- | ---------------------------------------------------------------------- |
| `spring.datasource.url`                            | JDBC URL for MariaDB (e.g., `jdbc:mariadb://localhost:3306/ecommerce`) |
| `spring.datasource.username`                       | Database user                                                          |
| `spring.datasource.password`                       | Database password                                                      |
| `spring.mail.host`                                 | SMTP host (e.g., `smtp.gmail.com`)                                     |
| `spring.mail.port`                                 | SMTP port (e.g., `587`)                                                |
| `spring.mail.username`                             | Sender Gmail address                                                   |
| `spring.mail.password`                             | Gmail App Password (16 characters)                                     |
| `spring.mail.properties.mail.smtp.auth`            | Enable SMTP auth (`true`)                                              |
| `spring.mail.properties.mail.smtp.starttls.enable` | Enable TLS (`true`)                                                    |
| `spring.servlet.multipart.max-file-size`           | Max single file upload size (e.g., `10MB`)                             |
| `spring.servlet.multipart.max-request-size`        | Max total multipart request size (e.g., `10MB`)                        |
| `razorpay.key.id`                                  | Razorpay API Key ID                                                    |
| `razorpay.secret.key`                              | Razorpay API Secret Key                                                |

---

## Folder Structure

```
Stella/
├── pom.xml
├── README.md
├── docs/
│   ├── ARCHITECTURE.md          # System design, ERD, and data flow
│   ├── API_REFERENCE.md         # All REST endpoints with payloads
│   └── BACKEND_FLOW.md          # Request lifecycle and internal execution paths
└── src/
    └── main/
        ├── java/com/nothing/stella/
        │   ├── StellaApplication.java       # Spring Boot entry point
        │   ├── controller/                  # REST controllers (request routing)
        │   ├── entity/                      # JPA entities (database tables)
        │   ├── model/                       # DTOs (request/response payloads)
        │   ├── repository/                  # Spring Data JPA repositories
        │   ├── services/                    # Business logic (interfaces + impls)
        │   ├── security/                    # JWT filter, entry point, UserDetailsService
        │   ├── exception/                   # Custom exception classes
        │   ├── exceptionHandler/            # Global @ControllerAdvice handler
        │   ├── errorResponse/               # Structured error response models
        │   └── miscellaneous/               # Input validators, email templates
        └── resources/
            ├── application.properties
            └── static/
                ├── products/                # Uploaded product images ({userId}/{name}/)
                └── storeLogos/              # Uploaded seller logos ({userId}/)
```

---

## Quick Links

| Document                                       | Description                                                                  |
| ---------------------------------------------- | ---------------------------------------------------------------------------- |
| [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md)   | System design, architecture diagrams, ERD, and key design patterns           |
| [docs/API_REFERENCE.md](docs/API_REFERENCE.md) | Full REST API reference with all endpoints and request/response payloads     |
| [docs/BACKEND_FLOW.md](docs/BACKEND_FLOW.md)   | Internal request lifecycle, execution flow diagrams, error handling strategy |

---

## Contact

**Aditya Kumawat**
Email: [kumawataditya105@gmail.com](mailto:kumawataditya105@gmail.com)
LinkedIn: [Aditya Kumawat](http://www.linkedin.com/in/adityakumawat105)
GitHub: [kumawat-aditya](http://github.com/kumawat-aditya)
