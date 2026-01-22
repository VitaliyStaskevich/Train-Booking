# Train Booking API

A REST API for train ticket booking and management, built with FastAPI and SQLite. This system enables users to search for trains, book tickets, manage their profile, and access customer support.

## Project Purpose

The Train Booking API is a backend service for a train ticket reservation platform. It provides:
* User authentication and account management
* Train search and availability checking
* Ticket booking and payment processing
* Passenger management for frequent travelers
* Customer support ticket system
* Administrative control panel for train inventory

---

## Technology Stack

### Backend Framework
* **FastAPI** (v0.115.0) 
* **Uvicorn** (v0.30.6) - ASGI server for running the application


### Database
* **SQLite** 
* **SQLAlchemy** (v2.0.36) - ORM 

### Authentication & Security
* **PyJWT** (v2.9.0) - JWT token generation and verification
* **passlib** (v1.7.4) - Password hashing utilities
* **bcrypt** (v4.0.1) - Secure password hashing

### Validation & Serialization
* **Pydantic** (v2.9.2) - Data validation and serialization

### Testing & Utilities
* **pytest** (v8.3.2) - Testing framework
* **httpx** (v0.27.0) - HTTP client for tests
* **python-dotenv** (v1.0.1) - Environment variable management

### Deployment
* **Docker** 

---

## Database Schema

The application uses 7 main tables:

### Users Table
 
| Column | Field Type | Null | Key | Default |
|--------|-----------|------|-----|---------|
| id | Integer | No | PRIMARY | Auto-increment |
| email | String | No | UNIQUE, INDEX | - |
| hashed_password | String | No | - | - |
| name | String | Yes | - | NULL |
| phone | String | Yes | - | NULL |
| city | String | Yes | - | NULL |
| is_admin | Boolean | No | - | False |

### Trains Table

| Column | Field Type | Null | Key | Default |
|--------|-----------|------|-----|---------|
| id | Integer | No | PRIMARY | Auto-increment |
| from_city | String | No | - | - |
| to_city | String | No | - | - |
| time | String | No | - | - |
| price | Float | No | - | - |

### Orders Table

| Column | Field Type | Null | Key | Default |
|--------|-----------|------|-----|---------|
| id | Integer | No | PRIMARY | Auto-increment |
| user_id | Integer | No | FOREIGN (users.id) | - |
| train_id | Integer | No | FOREIGN (trains.id) | - |
| passenger_name | String | Yes | - | NULL |
| passenger_age | Integer | Yes | - | NULL |
| paid | Boolean | No | - | False |

### Passangers Table

| Column | Field Type | Null | Key | Default |
|--------|-----------|------|-----|---------|
| id | Integer | No | PRIMARY | Auto-increment |
| user_id | Integer | Yes | FOREIGN (users.id) | NULL |
| name | String | No | - | - |
| age | Integer | No | - | - |

### Promotions Table

| Column | Field Type | Null | Key | Default |
|--------|-----------|------|-----|---------|
| id | Integer | No | PRIMARY | Auto-increment |
| title | String | No | - | - |
| description | Text | Yes | - | NULL |

### Support Tickets Table

| Column | Field Type | Null | Key | Default |
|--------|-----------|------|-----|---------|
| id | Integer | No | PRIMARY | Auto-increment |
| user_id | Integer | Yes | FOREIGN (users.id) | NULL |
| message | Text | No | - | - |
| resolved | Boolean | No | - | False |

## Prerequisites
* Python 3.11 or higher
* pip
* Docker (optional, for containerized deployment)

## Installation & setup
1. Clone the repository:
```
git clone https://github.com/VitaliyStaskevich/Train-Booking.git
cd Train-Booking
```
2. Install dependencies:
```
pip install -r requirements.txt
```
3. Create the database
```
cd app
py create_db.py
```
4. Create sample data (optional, for testing):
```
py fill.py
```

### Docker setup
1. Build the Docker image
```
docker build -t train-booking-api .
```

2. Run the container
```
docker run -p 8000:8000 -v $(pwd)/train_booking.db:/app/train_booking.db train-booking-api
```

This exposes the API on `http://localhost:8000` and persists the database

Starting the server for local development:
```
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

The API will be available at http://localhost:8000

### Interactive API Documentation
* **Swagger UI**: http://localhost:8000/docs
* **ReDoc**: http://localhost:8000/redoc

Most endpoints require JWT authentication.
Registration:
```
curl -X POST http://localhost:8000/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email": "user@example.com", "password": "mypassword"}'
```
Login:
```
curl -X POST http://localhost:8000/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "user@example.com", "password": "mypassword"}'
```
Returns a token to use in subsequent requests

### Testing
Run all testsL
```
pytests tests/ -v
```

The test suite automatically creates a clean test database, seeds sample data, and cleans up after execution.

### Configuration

Config settings are in `main.py`:
```
DATABASE_URL = "sqlite:///./train_booking.db"
JWT_SECRET = "placehplder_secret_key"
JWT_ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 60 * 24  
```
JWT_SECRET should be changed to a unique value in production.