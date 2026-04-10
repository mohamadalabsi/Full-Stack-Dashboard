# Full Stack Dashboard

An end-to-end customer dashboard built with a Spring Boot backend, a React frontend, PostgreSQL, JWT authentication, and AWS services for deployment and file storage.

This project is centered on the **React** frontend and the **Spring Boot** API. There is also an Angular frontend in the repository, but it is not the stack described or supported by this README.

## What it is for

The app is a customer management dashboard that lets users:

- sign up and log in
- browse customers
- create, update, and delete customer records
- upload and view customer profile images
- access protected dashboard pages with JWT-based authentication

It is designed as a full-stack example of how to build and deploy a modern dashboard using AWS and container-based delivery.

## Tech stack

### Backend

- Java 21
- Spring Boot 3
- Spring Web
- Spring Security
- Spring Data JPA / JDBC
- PostgreSQL
- Flyway database migrations
- JWT authentication with `Authorization` headers
- AWS SDK for S3
- Actuator for health/info endpoints
- Testcontainers for integration testing

### Frontend

- React 18
- Vite
- Chakra UI
- React Router
- Formik + Yup for forms and validation
- Axios for API calls
- `jwt-decode` for token handling
- `react-dropzone` for file upload UI

### Infrastructure / Deployment

- Docker
- Docker Compose
- AWS Elastic Beanstalk
- AWS S3 for customer profile images
- GitHub Actions CI/CD
- Docker Hub for container images

## Main features

- JWT login and signup flow
- Protected dashboard routes on the frontend
- Customer list and detail cards
- Create, edit, and delete customers
- Profile image upload and retrieval
- PostgreSQL schema management through Flyway migrations
- Health/info endpoints for observability

## Architecture overview

### Backend API

The Spring Boot API exposes REST endpoints under:

- `POST /api/v1/auth/login`
- `GET /api/v1/customers`
- `GET /api/v1/customers/{customerId}`
- `POST /api/v1/customers`
- `PUT /api/v1/customers/{customerId}`
- `DELETE /api/v1/customers/{customerId}`
- `POST /api/v1/customers/{customerId}/profile-image`
- `GET /api/v1/customers/{customerId}/profile-image`

Authentication is stateless. Successful login returns a JWT token, and the frontend stores it locally and sends it as a Bearer token on protected requests.

The backend also uses AWS S3 for customer profile images. In the current configuration, S3 can be mocked locally for development.

### Frontend

The React app provides:

- a login screen
- a signup flow
- a protected dashboard shell
- a customer management page
- modals/drawers for creating and updating customers
- card-based customer presentation with profile image actions

The frontend reads its API base URL from `VITE_API_BASE_URL`.

## Repository structure

```text
backend/               Spring Boot API
frontend/react/        React dashboard app
frontend/angular/      Legacy/alternative frontend (not covered here)
docker-compose.yml     Local container orchestration
Dockerrun.aws.json     AWS Elastic Beanstalk deployment package
```

## Local development

### Prerequisites

- Java 17
- Maven
- Node.js 18+ or 19+
- Docker and Docker Compose

### 1) Start PostgreSQL

From the repository root:

```bash
docker compose up -d db
```

The local database uses:

- host: `localhost`
- port: `5332`
- database: `customer`
- username: `amigoscode`
- password: `password`

### 2) Run the backend

```bash
cd backend
mvn spring-boot:run
```

The backend runs on `http://localhost:8080` by default.

### 3) Run the React frontend

```bash
cd frontend/react
npm install
echo VITE_API_BASE_URL=http://localhost:8080 > .env
npm run dev -- --host
```

The Vite app runs on `http://localhost:5173`.

## Docker Compose

The root `docker-compose.yml` defines:

- a PostgreSQL container
- the backend container
- the React container

It is intended for local orchestration and mirrors the app’s container-first setup.

## AWS deployment

The project includes Elastic Beanstalk deployment support through `Dockerrun.aws.json` and GitHub Actions.

### Backend delivery

The backend CI/CD workflow:

1. builds and tests the Spring Boot service
2. packages a Docker image with Jib
3. updates `Dockerrun.aws.json` with the new image tag
4. deploys to Elastic Beanstalk

### React delivery

The React workflow is present but disabled in the repo (`if: false`). It shows the intended flow for building a production image, injecting the backend URL, pushing to Docker Hub, and deploying via Elastic Beanstalk.

### S3 usage

Customer profile images are stored in S3 under a bucket configured in application properties. In development, the app can use a mocked S3 client.

## Configuration notes

Key backend settings are defined in `backend/src/main/resources/application.yml`:

- server port: `8080`
- CORS enabled for all origins/methods/headers in development
- PostgreSQL datasource configuration
- Flyway schema validation
- multipart upload limits for profile images
- AWS region and S3 bucket configuration

The React app expects:

- `VITE_API_BASE_URL` pointing to the backend API

## Database migrations

Flyway migrations create and evolve the customer table:

- `V1__Initial_Setup.sql` creates the base `customer` table
- `V2__Add_Customer_Profile_Image.sql` adds the profile image ID column

## API behavior highlights

- New customer registration issues a JWT token in the `Authorization` header.
- Login returns both the token and the authenticated customer payload.
- Protected API routes require a valid Bearer token.
- Profile image upload uses multipart form data.
- Profile image download returns `image/jpeg` content.

## Notes on the Angular folder

The repository still contains an Angular frontend under `frontend/angular/`. That code is not the focus of this README and can be treated as legacy or alternate client code.

## License

No explicit license file was found in the repository root.

