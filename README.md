# 🧾 Invoice Tracker System

[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.x-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![Java](https://img.shields.io/badge/Java-17+-orange.svg)](https://www.oracle.com/java/)


A comprehensive Spring Boot application for managing invoices with multi-format upload support, role-based access control, and complete audit trail functionality.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Technology Stack](#technology-stack)
- [Getting Started](#getting-started)
- [Database Setup](#database-setup)
- [Configuration](#configuration)
- [API Documentation](#api-documentation)
- [User Roles & Permissions](#user-roles--permissions)
- [Design Patterns](#design-patterns)
- [Security](#security)
- [Project Structure](#project-structure)
- [Contributing](#contributing)
- [License](#license)

---

## 🎯 Overview

Invoice Tracker is a full-stack web application designed to streamline invoice management for organizations. It supports multiple file formats (PDF, images, and web forms), provides role-based access control for different user types, and maintains a complete audit trail of all operations.

### Key Capabilities

- **Multi-format Invoice Upload**: Support for PDF, JPEG, PNG, GIF, and web form submissions
- **Role-Based Access Control**: Three distinct roles (USER, AUDITOR, SUPERUSER) with granular permissions
- **Complete Audit Trail**: Track all changes with before/after states for compliance
- **Advanced Search & Filtering**: Search by date, file type, status, and metadata
- **Secure File Storage**: UUID-based file naming with validation and size limits
- **RESTful API Architecture**: 34 well-documented endpoints following REST best practices

---

## ✨ Features

### For End Users (USER Role)
- ✅ Create invoices with file upload or web form
- ✅ View, edit, and delete own invoices
- ✅ Link multiple products to invoices
- ✅ Download invoice files
- ✅ View personal statistics and audit logs
- ✅ Search and filter own invoices

### For Auditors (AUDITOR Role)
- 👁️ View all invoices across the system (read-only)
- 👁️ Access complete audit logs
- 👁️ Download any invoice file
- 👁️ View system-wide statistics
- 👁️ View all users (read-only)

### For Administrators (SUPERUSER Role)
- ⚡ All USER and AUDITOR capabilities
- ⚡ Manage users (create, update, delete, assign roles)
- ⚡ Manage categories and products
- ⚡ Edit and delete any invoice
- ⚡ Change invoice status (PENDING → APPROVED/REJECTED/COMPLETED)
- ⚡ Create invoices on behalf of other users

---

## 🏗️ Architecture

The application follows a **Layered Architecture** pattern with clear separation of concerns:

```
┌─────────────────────────────────────────┐
│         Presentation Layer              │
│  (Controllers, DTOs, Exception Handlers)│
└─────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────┐
│         Business Logic Layer            │
│    (Services, Factories, Strategies)    │
└─────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────┐
│         Data Access Layer               │
│     (Repositories, JPA Entities)        │
└─────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────┐
│            Database Layer               │
│            (MySQL)                 │
└─────────────────────────────────────────┘
```

### Design Patterns Implemented

1. **Factory Pattern** - Invoice creation for different input types
2. **Strategy Pattern** - File processing for different file types
3. **Repository Pattern** - Data access abstraction with Spring Data JPA
4. **Builder Pattern** - Clean object construction using Lombok
5. **DTO Pattern** - Separation between entities and API contracts

---

## 🛠️ Technology Stack

### Backend
- **Java 17+** - Programming language
- **Spring Boot 3.x** - Application framework
- **Spring Security** - Authentication & authorization
- **Spring Data JPA** - Data persistence
- **Hibernate** - ORM framework
- **PostgreSQL 14+** - Relational database
- **JWT (jjwt)** - Token-based authentication
- **Lombok** - Boilerplate reduction
- **Maven** - Build tool

### Frontend
- **React.js** - UI framework
- **Axios** - HTTP client
- **React Router** - Navigation

### Security
- JWT authentication with HS512 algorithm
- BCrypt password encryption
- Role-based access control (RBAC)
- Rate limiting for login attempts
- File validation (type, size, content)

---

## 🚀 Getting Started

### Prerequisites

- Java 17 or higher
- Maven 3.8+
- PostgreSQL 14+
- Node.js 16+ (for frontend)
- Git

### Installation Steps

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/invoice-tracker.git
   cd invoice-tracker
   ```

2. **Configure Database**
   
   Create a PostgreSQL database:
   ```sql
   CREATE DATABASE invoice_tracker;
   ```

3. **Update Application Properties**
   
   Edit `src/main/resources/application.properties`:
   ```properties
   # Database Configuration
   spring.datasource.url=jdbc:postgresql://localhost:5432/invoice_tracker
   spring.datasource.username=your_username
   spring.datasource.password=your_password
   
   # File Storage
   file.upload-dir=./uploads
   
   # JWT Configuration
   jwt.secret=your-secret-key-here
   jwt.expiration=86400000
   ```

4. **Build the project**
   ```bash
   mvn clean install
   ```

5. **Run the application**
   ```bash
   mvn spring-boot:run
   ```

6. **Access the application**
   - Backend API: `http://localhost:8080`
   - Frontend: `http://localhost:3000` (if running separately)

### Default Super User

On first startup, a default superuser is created:
- **Username**: `superuser`
- **Password**: `SuperUser123!`
- **Email**: `superuser@system.com`
- **Roles**: USER, AUDITOR, SUPERUSER

⚠️ **Important**: Change this password after first login!

---

## 💾 Database Setup

### Database Schema

The application uses 6 main tables:

```
User ──┬─→ Invoice ──→ InvoiceProduct ──→ Product ──→ Category
       │              └─→ Log
       └─────────────────→ (performedBy)
```

### Entity Relationships

- **User** has many **Invoices** (One-to-Many)
- **User** has many **Roles** (Many-to-Many)
- **Invoice** has many **InvoiceProducts** (One-to-Many)
- **Invoice** has many **Logs** (One-to-Many)
- **Product** belongs to one **Category** (Many-to-One)
- **InvoiceProduct** links **Invoice** and **Product** (Junction table)

### Tables

#### User
```sql
userId (PK, VARCHAR(20))
username (UNIQUE)
email (UNIQUE)
password (BCrypt encrypted)
isActive (BOOLEAN)
createdAt, updatedAt
```

#### Invoice
```sql
invoiceId (PK, BIGSERIAL)
uploadedByUserId (FK → User)
invoiceDate (DATE)
fileType (ENUM: PDF, IMAGE, WEB_FORM)
fileName, storedFileName, originalFileName
fileSize (BIGINT)
totalAmount (DOUBLE)
status (ENUM: PENDING, APPROVED, REJECTED, COMPLETED)
isActive (BOOLEAN)
createdAt, updatedAt
```

#### Log (Audit Trail)
```sql
logId (PK, BIGSERIAL)
invoiceId (FK → Invoice)
performedBy (FK → User)
actionType (ENUM: CREATE, UPDATE, DELETE, VIEW, DOWNLOAD)
timestamp (TIMESTAMP)
oldValues (JSON)
newValues (JSON)
```

---

## ⚙️ Configuration

### Application Properties

```properties
# Server Configuration
server.port=8080

# Database
spring.datasource.url=jdbc:postgresql://localhost:5432/invoice_tracker
spring.datasource.username=postgres
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

# File Upload
spring.servlet.multipart.max-file-size=10MB
spring.servlet.multipart.max-request-size=10MB
file.upload-dir=./uploads

# JWT
jwt.secret=your-256-bit-secret-key-here
jwt.expiration=86400000

# CORS
cors.allowed-origins=http://localhost:3000
```

### Environment Variables

Alternatively, use environment variables:

```bash
export DB_URL=jdbc:postgresql://localhost:5432/invoice_tracker
export DB_USERNAME=postgres
export DB_PASSWORD=your_password
export JWT_SECRET=your_secret_key
export UPLOAD_DIR=/path/to/uploads
```

---

## 📚 API Documentation

### Base URL
```
http://localhost:8080
```

### Authentication Endpoints

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| POST | `/auth/signup` | Register new user | Public |
| POST | `/auth/login` | Login user | Public |
| GET | `/auth/verify` | Verify JWT token | Required |

### User Management Endpoints

| Method | Endpoint | Description | Role |
|--------|----------|-------------|------|
| POST | `/users` | Create user | SUPERUSER |
| GET | `/users` | List all users | SUPERUSER |
| GET | `/users/{username}` | Get user details | SUPERUSER, AUDITOR, SELF |
| PUT | `/users/{username}` | Update user | SUPERUSER, SELF |
| DELETE | `/users/{username}` | Delete user | SUPERUSER |
| PUT | `/users/{username}/roles` | Update user roles | SUPERUSER |
| PUT | `/users/{username}/status` | Change user status | SUPERUSER |
| PUT | `/users/{username}/password` | Change password | SUPERUSER, SELF |

### Invoice Endpoints

| Method | Endpoint | Description | Role |
|--------|----------|-------------|------|
| POST | `/invoices` | Create invoice (JSON) | USER, SUPERUSER |
| POST | `/invoices/upload` | Create invoice (File) | USER, SUPERUSER |
| GET | `/invoices/{id}` | Get invoice by ID | OWNER, SUPERUSER, AUDITOR |
| GET | `/invoices` | List user's invoices | USER, SUPERUSER, AUDITOR |
| GET | `/invoices/all` | List all invoices | SUPERUSER, AUDITOR |
| GET | `/invoices/search` | Search invoices | USER, SUPERUSER, AUDITOR |
| GET | `/invoices/stats` | Get statistics | USER, SUPERUSER, AUDITOR |
| GET | `/invoices/{id}/audit-logs` | Get audit logs | OWNER, SUPERUSER, AUDITOR |
| GET | `/invoices/{id}/download` | Download file | OWNER, SUPERUSER, AUDITOR |
| PUT | `/invoices/{id}` | Update invoice | OWNER, SUPERUSER |
| PUT | `/invoices/{id}/full` | Full update + status | OWNER, SUPERUSER |
| PUT | `/invoices/{id}/upload` | Update with file | OWNER, SUPERUSER |
| DELETE | `/invoices/{id}` | Delete invoice | OWNER, SUPERUSER |

### Example Request

**Login:**
```bash
curl -X POST http://localhost:8080/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "username": "superuser",
    "password": "SuperUser123!"
  }'
```

**Response:**
```json
{
  "token": "eyJhbGciOiJIUzUxMiJ9...",
  "user": {
    "userId": "SUPER001",
    "username": "superuser",
    "email": "superuser@system.com",
    "roles": ["USER", "AUDITOR", "SUPERUSER"]
  }
}
```

**Create Invoice:**
```bash
curl -X POST http://localhost:8080/invoices/upload \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -F "file=@invoice.pdf" \
  -F "invoiceDate=2024-10-31" \
  -F "productIds=1,2,3" \
  -F "productQuantities={\"1\":5.0,\"2\":3.0,\"3\":10.0}"
```

---

## 🔐 User Roles & Permissions

### 👤 USER
**Capabilities:**
- Create invoices (own)
- View/Edit/Delete own invoices only
- Upload files (PDF, Images)
- Link products to invoices
- Download own invoice files
- View personal statistics
- Access own audit logs

**Restrictions:**
- Cannot view other users' invoices
- Cannot manage users, categories, or products
- Cannot change invoice status

### 👁️ AUDITOR
**Capabilities:**
- View ALL invoices (read-only)
- View ALL users (read-only)
- Download any invoice file
- Access all audit logs
- View system-wide statistics

**Restrictions:**
- Cannot create, edit, or delete anything
- Read-only access across the system

### ⚡ SUPERUSER
**Capabilities:**
- **Everything USER can do**
- **Everything AUDITOR can do**
- Plus:
  - Create/Edit/Delete users
  - Assign/Remove roles
  - Create/Edit/Delete categories
  - Create/Edit/Delete products
  - Edit/Delete ANY invoice
  - Change invoice status
  - Create invoices for other users
  - Activate/Deactivate users

---

## 🎨 Design Patterns

### 1. Factory Pattern
**Location**: `com.example.invoicetracker.factory`

**Purpose**: Create different types of invoices based on input

```java
public interface InvoiceCreator {
    Invoice createInvoice(InvoiceRequest request, User user);
    boolean supports(InvoiceRequest request);
}

// Implementations:
// - FileInvoiceCreator: For file uploads
// - FormInvoiceCreator: For web form submissions
```

### 2. Strategy Pattern
**Location**: `com.example.invoicetracker.strategy`

**Purpose**: Process different file types with specific logic

```java
public interface FileProcessingStrategy {
    FileProcessingResult process(MultipartFile file);
    boolean supports(String fileType);
}

// Implementations:
// - PdfProcessingStrategy: For PDF files
// - ImageProcessingStrategy: For image files
```

### 3. Repository Pattern
**Location**: `com.example.invoicetracker.repository`

**Purpose**: Abstract data access layer

```java
public interface InvoiceRepository extends JpaRepository<Invoice, Long> {
    Page<Invoice> findAllByIsActiveTrue(Pageable pageable);
    // Custom queries...
}
```

### 4. Builder Pattern
**Usage**: Throughout entities and DTOs with Lombok

```java
@Builder
public class Invoice {
    // Enables: Invoice.builder().field(value).build()
}
```

### 5. DTO Pattern
**Location**: `com.example.invoicetracker.dto`

**Purpose**: Separate API contracts from domain entities

```java
// Request DTOs: InvoiceRequest, UserRequest
// Response DTOs: InvoiceResponse, UserResponse
```

---

## 🔒 Security

### Authentication Flow

1. User sends credentials to `/auth/login`
2. Server validates and generates JWT token
3. Token contains username and roles
4. Client stores token (localStorage/sessionStorage)
5. Client sends token in `Authorization` header for subsequent requests
6. Server validates token and extracts user info

### JWT Token Structure

```json
{
  "sub": "username",
  "roles": ["ROLE_USER", "ROLE_SUPERUSER"],
  "iat": 1698765432,
  "exp": 1698851832
}
```

### Security Features

✅ **Password Encryption**: BCrypt with strength 10
✅ **JWT Tokens**: HS512 algorithm, 24-hour expiration
✅ **Rate Limiting**: Max 5 login attempts, 5-minute lockout
✅ **File Validation**: Type, size, and extension checking
✅ **CORS Configuration**: Configured for frontend origin
✅ **SQL Injection Protection**: JPA parameterized queries
✅ **XSS Protection**: Input validation and sanitization

### Rate Limiting

The system implements login attempt rate limiting:
- **Max Attempts**: 5 failed login attempts
- **Lock Duration**: 5 minutes
- **Tracking**: By username
- **Reset**: Automatic on successful login

---

## 📁 Project Structure

```
invoice-tracker/
├── src/
│   ├── main/
│   │   ├── java/com/example/invoicetracker/
│   │   │   ├── config/              # Configuration classes
│   │   │   │   ├── DataInitializer.java
│   │   │   │   ├── FileValidator.java
│   │   │   │   └── SecurityConfig.java
│   │   │   ├── controller/          # REST Controllers
│   │   │   │   ├── AuthController.java
│   │   │   │   ├── UserController.java
│   │   │   │   ├── CategoryController.java
│   │   │   │   ├── ProductController.java
│   │   │   │   └── InvoiceController.java
│   │   │   ├── dto/                 # Data Transfer Objects
│   │   │   │   ├── request/
│   │   │   │   └── response/
│   │   │   ├── exception/           # Custom Exceptions
│   │   │   │   └── GlobalExceptionHandler.java
│   │   │   ├── factory/             # Factory Pattern
│   │   │   │   ├── InvoiceCreator.java
│   │   │   │   ├── FileInvoiceCreator.java
│   │   │   │   ├── FormInvoiceCreator.java
│   │   │   │   └── InvoiceFactory.java
│   │   │   ├── model/               # Domain Entities
│   │   │   │   ├── entity/
│   │   │   │   │   ├── User.java
│   │   │   │   │   ├── Role.java
│   │   │   │   │   ├── Category.java
│   │   │   │   │   ├── Product.java
│   │   │   │   │   ├── Invoice.java
│   │   │   │   │   ├── InvoiceProduct.java
│   │   │   │   │   └── Log.java
│   │   │   │   ├── enums/
│   │   │   │   │   ├── ActionType.java
│   │   │   │   │   ├── FileType.java
│   │   │   │   │   └── InvoiceStatus.java
│   │   │   │   └── converter/
│   │   │   ├── repository/          # JPA Repositories
│   │   │   ├── security/            # Security Components
│   │   │   │   ├── JwtAuthenticationFilter.java
│   │   │   │   └── JwtUtil.java
│   │   │   ├── service/             # Business Logic
│   │   │   │   ├── AuthService.java
│   │   │   │   ├── UserService.java
│   │   │   │   ├── CategoryService.java
│   │   │   │   ├── ProductService.java
│   │   │   │   ├── InvoiceService.java
│   │   │   │   ├── AuditLogService.java
│   │   │   │   ├── FileStorageService.java
│   │   │   │   └── LoginAttemptService.java
│   │   │   ├── strategy/            # Strategy Pattern
│   │   │   │   ├── FileProcessingStrategy.java
│   │   │   │   ├── PdfProcessingStrategy.java
│   │   │   │   ├── ImageProcessingStrategy.java
│   │   │   │   └── FileProcessor.java
│   │   │   └── InvoiceTrackerApplication.java
│   │   └── resources/
│   │       ├── application.properties
│   │       └── static/
│   └── test/                        # Unit & Integration Tests
├── uploads/                         # File Storage Directory
├── pom.xml                          # Maven Configuration
└── README.md
```

---

## 🧪 Testing

### Run Tests

```bash
# Run all tests
mvn test

# Run specific test class
mvn test -Dtest=InvoiceServiceTest

# Run with coverage
mvn clean test jacoco:report
```

### Test Structure

```
src/test/java/
├── controller/          # Controller tests
├── service/             # Service layer tests
├── repository/          # Repository tests
└── integration/         # Integration tests
```

---

## 🔄 Git Workflow

This project follows a structured Git branching strategy:

### Branch Structure

- `main` - Production-ready code
- `dev` - Development branch
- `feature/*` - Feature branches (e.g., `feature/invoice-upload`)
- `bugfix/*` - Bug fix branches
- `hotfix/*` - Emergency fixes

### Workflow

1. Create feature branch from `dev`:
   ```bash
   git checkout dev
   git pull origin dev
   git checkout -b feature/your-feature-name
   ```

2. Make changes and commit:
   ```bash
   git add .
   git commit -m "feat: add invoice upload functionality"
   ```

3. Push to remote:
   ```bash
   git push origin feature/your-feature-name
   ```

4. Create Pull Request to `dev`
5. After code review and approval, merge to `dev`
6. When ready for release, create PR from `dev` to `main`

### Commit Message Convention

```
feat: Add new feature
fix: Fix bug
docs: Update documentation
style: Code formatting
refactor: Code restructuring
test: Add tests
chore: Maintenance tasks
```

---

## 🚀 Deployment

### Local Deployment

1. Build the JAR:
   ```bash
   mvn clean package -DskipTests
   ```

2. Run the JAR:
   ```bash
   java -jar target/invoice-tracker-0.0.1-SNAPSHOT.jar
   ```

### Docker Deployment

```dockerfile
FROM openjdk:17-alpine
COPY target/invoice-tracker-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","/app.jar"]
```

Build and run:
```bash
docker build -t invoice-tracker .
docker run -p 8080:8080 invoice-tracker
```

---

## 🐛 Troubleshooting

### Common Issues

**Issue: Database connection failed**
```
Solution: Check PostgreSQL is running and credentials are correct in application.properties
```

**Issue: File upload fails**
```
Solution: Ensure upload directory exists and has write permissions
```

**Issue: JWT token invalid**
```
Solution: Token may be expired (24h), login again to get new token
```

**Issue: Port 8080 already in use**
```
Solution: Change port in application.properties: server.port=8081
```

---

## 📞 Support & Contact

For questions, issues, or contributions:

- **Email**: your.email@example.com
- **GitHub Issues**: [Create an issue](https://github.com/yourusername/invoice-tracker/issues)
- **Documentation**: [Wiki](https://github.com/yourusername/invoice-tracker/wiki)

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- Spring Boot Team for the excellent framework
- PostgreSQL Community
- All contributors and testers

---

## 📊 Project Statistics

- **Total Lines of Code**: ~8,000+
- **REST API Endpoints**: 34
- **Database Tables**: 6
- **Design Patterns**: 5
- **Security Features**: 4 layers
- **Supported File Types**: 4 (PDF, JPEG, PNG, GIF)

---

**Built with ❤️ using Spring Boot**
