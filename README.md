# Full-Stack Customer Management Application

A full-stack web application for managing customer records, with user authentication, customer registration, profile image uploads, and CRUD operations. The project uses React for the frontend, Spring Boot for the backend REST API, and PostgreSQL for persistent data storage.

## Features

- **User Authentication:** Login and registration with JWT-based authentication.
- **Customer Management:** Create, retrieve, update, and delete customer records.
- **Customer Profiles:** Upload and retrieve customer profile images.
- **REST API:** Backend endpoints built with Spring Boot.
- **Database Migrations:** Flyway manages database schema changes.
- **Responsive UI:** React components styled with Chakra UI.
- **Protected Routes:** Restrict access to dashboard pages using authentication.
- **Containerization:** Docker and Docker Compose support local development.
- **Cloud Integration:** AWS S3 integration for profile image storage, with mock storage configuration available.
- **Automated Testing:** Spring Boot testing infrastructure, including Testcontainers support.

## Tech Stack

| Layer | Technologies |
|---|---|
| Frontend | React 18, JavaScript, Vite |
| UI | Chakra UI, Emotion, React Icons |
| Routing | React Router |
| Forms and Validation | Formik, Yup |
| HTTP Client | Axios |
| Backend | Java 17, Spring Boot 3 |
| API | Spring Web, REST |
| Security | Spring Security, JWT |
| Database | PostgreSQL |
| Data Access | Spring Data JPA, JDBC |
| Database Migrations | Flyway |
| Cloud Storage | AWS S3 SDK |
| Containers | Docker, Docker Compose |
| Testing | JUnit, Mockito, Testcontainers |
| Additional Frontend | Angular 15, TypeScript |

## Architecture

The application follows a frontend-backend architecture.

```text
                 User
                   |
                   v
        React Frontend (Vite)
                   |
              HTTP / REST
                   |
                   v
        Spring Boot REST API
                   |
          +--------+---------+
          |                  |
          v                  v
   Spring Security       Customer Services
   JWT Authentication        |
                             v
                   JPA / JDBC Repositories
                             |
                             v
                       PostgreSQL

       Spring Boot API ---> AWS S3
                           (optional
                        image storage)
```

The frontend communicates with the backend using Axios. The backend processes requests, applies business logic, interacts with PostgreSQL, and returns API responses. JWT tokens are used to authenticate protected requests.

## Project Structure

```text
full-stack-professional-1/
├── backend/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/amigoscode/
│   │   │   │   ├── auth/
│   │   │   │   ├── customer/
│   │   │   │   ├── exception/
│   │   │   │   ├── jwt/
│   │   │   │   ├── s3/
│   │   │   │   └── security/
│   │   │   └── resources/
│   │   │       ├── db/migration/
│   │   │       └── application.yml
│   │   └── test/
│   └── pom.xml
├── frontend/
│   ├── react/
│   │   ├── src/
│   │   │   ├── components/
│   │   │   ├── services/
│   │   │   ├── Customer.jsx
│   │   │   ├── Home.jsx
│   │   │   └── main.jsx
│   │   └── package.json
│   └── angular/
├── .github/
│   └── workflows/
├── .ci/
├── docker-compose.yml
└── README.md
```

## Getting Started

### Prerequisites

Install the following tools:

- [Git](https://git-scm.com/)
- [Java 17](https://adoptium.net/)
- [Maven](https://maven.apache.org/)
- [Node.js and npm](https://nodejs.org/)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- PostgreSQL, or use the provided Docker Compose configuration

### 1. Clone the Repository

```bash
git clone <YOUR_REPOSITORY_URL>
cd full-stack-professional-1
```

Replace `<YOUR_REPOSITORY_URL>` with your GitHub repository URL.

### 2. Start PostgreSQL

The provided Docker Compose configuration starts PostgreSQL on host port `5332`.

```bash
docker compose up -d db
```

The default configuration uses:

- Database: `customer`
- Username: `bsrini20`
- Password: `password`
- Host port: `5332`

**Security note:** These are development defaults from the supplied configuration. Do not use them in a production deployment.

### 3. Start the Backend

Open a terminal in the project root:

```bash
cd backend
mvn spring-boot:run
```

The Spring Boot API is configured to run on:

```text
http://localhost:8080
```

Flyway migrations initialize the database schema. The application expects the database to be available and its schema to match the JPA entities.

### 4. Start the React Frontend

Open another terminal:

```bash
cd frontend/react
npm install
```

Create a `.env` file inside `frontend/react`:

```env
VITE_API_BASE_URL=http://localhost:8080
```

Start the development server:

```bash
npm run dev
```

Open the local URL printed by Vite in your terminal, typically:

```text
http://localhost:5173
```

The frontend uses `VITE_API_BASE_URL` to communicate with the backend. Ensure that the backend CORS configuration permits the frontend origin.

## Application Pages

| Page | Route | Purpose |
|---|---|---|
| Login | `/` | Authenticate an existing user |
| Sign Up | `/signup` | Register a customer account |
| Dashboard | `/dashboard` | Access the application dashboard |
| Customer Management | `/dashboard/customers` | View and manage customer records |

Dashboard routes are wrapped in a protected-route component.

## REST API

The backend exposes the following main endpoints.

### Authentication

| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/v1/auth/login` | Authenticate a user and return a JWT response |

### Customers

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/v1/customers` | Retrieve all customers |
| GET | `/api/v1/customers/{customerId}` | Retrieve a customer by ID |
| POST | `/api/v1/customers` | Register a new customer |
| PUT | `/api/v1/customers/{customerId}` | Update a customer |
| DELETE | `/api/v1/customers/{customerId}` | Delete a customer |
| POST | `/api/v1/customers/{customerId}/profile-image` | Upload a customer profile image |
| GET | `/api/v1/customers/{customerId}/profile-image` | Retrieve a customer profile image |

Protected requests use the HTTP `Authorization` header with a bearer token:

```http
Authorization: Bearer <JWT_TOKEN>
```

The profile image upload endpoint accepts multipart form data with the field name `file`.

## Database and Migrations

Database schema migrations are maintained in:

```text
backend/src/main/resources/db/migration/
```

The project uses Flyway to apply versioned SQL migrations and Hibernate to validate the database schema against the application's entities.

To inspect the PostgreSQL container logs:

```bash
docker compose logs db
```

To connect to the database from the terminal:

```bash
docker exec -it postgres psql -U bsrini20 -d customer
```

## Running Tests

Run backend tests from the `backend` directory:

```bash
mvn test
```

The backend includes testing dependencies for unit and integration testing. Some integration tests may require Docker and a functioning container runtime.

Build the React frontend:

```bash
cd frontend/react
npm run build
```

## Docker

The repository includes a `docker-compose.yml` file for running a containerized application environment.

Start the configured services:

```bash
docker compose up --build
```

Stop the services:

```bash
docker compose down
```

To remove the database's persisted Docker volume as well:

```bash
docker compose down -v
```

**Important:** The supplied Compose file references prebuilt backend and frontend images and configures the React build separately. Its API image, frontend image, environment variables, and port mappings may need adjustment to run the checked-out source code end to end. Follow the individual backend and frontend instructions above for local source-based development.

## Environment Configuration

The backend's default settings are defined in:

```text
backend/src/main/resources/application.yml
```

The frontend API URL is configured through:

```text
frontend/react/.env
```

For production, move database credentials, JWT signing secrets, and AWS credentials into environment variables or a secrets manager. Never commit real credentials or secret keys to source control.

## Future Improvements

- Add automated frontend tests.
- Add API documentation using OpenAPI/Swagger.
- Improve application-wide error handling and validation.
- Add pagination and filtering for customer records.
- Add role-based authorization for administrative operations.
- Configure CI/CD to build, test, and deploy the application.

## Learning Objectives

This project demonstrates practical full-stack development concepts, including:

- Designing REST APIs with Spring Boot.
- Building reusable React components.
- Managing authentication and authorization with JWT.
- Persisting application data using PostgreSQL.
- Implementing database migrations with Flyway.
- Integrating frontend and backend services.
- Testing backend components and containerized database integrations.
- Preparing applications for Docker-based deployment.

## Acknowledgments

This project is based on the [Amigoscode Full Stack Professional](https://amigoscode.com/) course, which provided the foundation for learning and implementing full-stack application development using React, Spring Boot, PostgreSQL, and Docker.

The project serves as a learning and development exercise to explore full-stack architecture, REST API development, authentication, database integration, and containerization.

**Original course and implementation:** Amigoscode.

**Personal contributions:** Enhancements, additional features, bug fixes, testing, documentation, and architectural improvements will be documented here as they are implemented.

This repository is intended for educational and portfolio purposes. All original course materials and third-party components remain subject to their respective licenses and terms.


