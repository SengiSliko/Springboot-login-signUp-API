# Login Sign Up API

A Spring Boot REST API for user authentication with JWT tokens and PostgreSQL database.

## Prerequisites

- Java 17 or higher
- Maven 3.6+
- PostgreSQL 12+

## PostgreSQL Database Setup (Using pgAdmin 4)

### Step 1: Open pgAdmin 4

1. Launch **pgAdmin 4** from your applications
2. If prompted, set a master password (remember this for future sessions)

### Step 2: Connect to PostgreSQL Server

1. In the left sidebar, expand **Servers**
2. If no server exists, right-click **Servers** → **Register** → **Server...**
3. In the **General** tab:
   - **Name**: `Local PostgreSQL` (or any name you prefer)
4. In the **Connection** tab:
   - **Host name/address**: `localhost`
   - **Port**: `5432`
   - **Maintenance database**: `postgres`
   - **Username**: `postgres`
   - **Password**: Your PostgreSQL password
   - Check **Save password** if desired
5. Click **Save**

### Step 3: Create the Database

1. In the left sidebar, expand your server connection
2. Right-click on **Databases** → **Create** → **Database...**
3. In the **General** tab:
   - **Database**: `authdb`
   - **Owner**: `postgres` (or your username)
4. Click **Save**

You should now see `authdb` under Databases in the sidebar.

### Step 4: Verify Connection Details

To find your connection details in pgAdmin:

1. Right-click on your server → **Properties**
2. Go to the **Connection** tab
3. Note down:
   - **Host**: (usually `localhost`)
   - **Port**: (usually `5432`)
   - **Username**: (usually `postgres`)

### Step 5: Configure the Spring Boot Application

Edit `src/main/resources/application.properties` with your pgAdmin connection details:

```properties
# PostgreSQL Database
spring.datasource.url=jdbc:postgresql://localhost:5432/authdb
spring.datasource.username=postgres
spring.datasource.password=YOUR_PGADMIN_PASSWORD
```

Replace `YOUR_PGADMIN_PASSWORD` with the password you use to connect in pgAdmin.

| Property | Description | Example |
|----------|-------------|---------|
| `spring.datasource.url` | JDBC connection URL | `jdbc:postgresql://localhost:5432/authdb` |
| `spring.datasource.username` | PostgreSQL username from pgAdmin | `postgres` |
| `spring.datasource.password` | PostgreSQL password from pgAdmin | `mypassword123` |

### Step 6: Generate a Secure JWT Secret

Generate a Base64-encoded secret key (minimum 256 bits).

**Option A - Using Terminal:**
```bash
openssl rand -base64 32
```

**Option B - Online Generator:**
Use any secure random string generator and ensure it's at least 32 characters.

Set it in `application.properties`:
```properties
jwt.secret=YOUR_GENERATED_SECRET_HERE
```

Or set as environment variable (Terminal):
```bash
export JWT_SECRET=YOUR_GENERATED_SECRET_HERE
```

### Step 7: Verify Database Tables (After Running the App)

After running the application for the first time, you can verify the tables were created:

1. In pgAdmin, expand: **Servers** → **Your Server** → **Databases** → **authdb** → **Schemas** → **public** → **Tables**
2. You should see a `users` table
3. Right-click `users` → **View/Edit Data** → **All Rows** to see registered users

## Running the Application

### Option 1: Maven Wrapper
```bash
cd "login sign up API"
./mvnw spring-boot:run
```

### Option 2: Maven
```bash
cd "login sign up API"
mvn spring-boot:run
```

### Option 3: JAR file
```bash
./mvnw clean package
java -jar target/auth-api-0.0.1-SNAPSHOT.jar
```

The API will start on `http://localhost:8080`

## Swagger UI

Once the application is running, access the interactive API documentation:

- **Swagger UI**: http://localhost:8080/swagger-ui.html
- **OpenAPI JSON**: http://localhost:8080/v3/api-docs

### Using Swagger UI

1. Open http://localhost:8080/swagger-ui.html in your browser
2. You'll see all available endpoints documented
3. To test authenticated endpoints:
   - First, use the `/api/auth/register` or `/api/auth/login` endpoint
   - Copy the `token` from the response
   - Click the **Authorize** button (lock icon) at the top
   - Enter your token (without "Bearer " prefix)
   - Click **Authorize** and then **Close**
   - Now you can test protected endpoints

## Testing with Postman

### Import Collection

1. Open Postman
2. Create a new Collection called "Auth API"
3. Add the following requests:

### Register Request
- **Method**: POST
- **URL**: `http://localhost:8080/api/auth/register`
- **Headers**: `Content-Type: application/json`
- **Body** (raw JSON):
```json
{
    "firstName": "John",
    "lastName": "Doe",
    "email": "john@example.com",
    "password": "securepassword123"
}
```

### Login Request
- **Method**: POST
- **URL**: `http://localhost:8080/api/auth/login`
- **Headers**: `Content-Type: application/json`
- **Body** (raw JSON):
```json
{
    "email": "john@example.com",
    "password": "securepassword123"
}
```

### Using JWT Token in Postman
1. After login, copy the `token` from the response
2. For protected endpoints, go to the **Authorization** tab
3. Select **Type**: `Bearer Token`
4. Paste your token in the **Token** field

## API Endpoints

### Register a New User

**POST** `/api/auth/register`

```bash
curl -X POST http://localhost:8080/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "John",
    "lastName": "Doe",
    "email": "john@example.com",
    "password": "securepassword123"
  }'
```

**Response:**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "email": "john@example.com",
  "firstName": "John",
  "lastName": "Doe",
  "role": "USER"
}
```

### Login

**POST** `/api/auth/login`

```bash
curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com",
    "password": "securepassword123"
  }'
```

**Response:**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "email": "john@example.com",
  "firstName": "John",
  "lastName": "Doe",
  "role": "USER"
}
```

### Using the JWT Token

Include the token in the Authorization header for protected endpoints:

```bash
curl -X GET http://localhost:8080/api/protected-endpoint \
  -H "Authorization: Bearer YOUR_JWT_TOKEN"
```

## Troubleshooting

### Connection Refused
```
Connection to localhost:5432 refused
```
**Solution:**
- Ensure PostgreSQL is running
- Open pgAdmin 4 - if it can connect to the server, PostgreSQL is running
- If pgAdmin shows the server as disconnected, right-click → **Connect Server**
- On macOS: `brew services start postgresql`
- On Windows: Open Services app → find "postgresql" → Start

### Authentication Failed
```
FATAL: password authentication failed for user "postgres"
```
**Solution:**
- Verify the password in `application.properties` matches your pgAdmin password
- In pgAdmin, right-click your server → **Properties** → **Connection** to verify credentials
- To reset password in pgAdmin:
  1. Expand server → **Login/Group Roles** → right-click `postgres` → **Properties**
  2. Go to **Definition** tab → enter new password → **Save**

### Database Does Not Exist
```
FATAL: database "authdb" does not exist
```
**Solution:**
- In pgAdmin: Right-click **Databases** → **Create** → **Database** → name it `authdb`

### Permission Denied
```
permission denied for table users
```
**Solution in pgAdmin:**
1. Right-click on `authdb` → **Query Tool**
2. Run:
   ```sql
   GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO postgres;
   ```
3. Click the **Execute** button (or press F5)

### Cannot Find the Users Table
**Solution:**
- The table is created automatically when you first run the application
- Make sure Flyway migration ran successfully (check application logs)
- In pgAdmin, right-click on **Tables** → **Refresh**

## Project Structure

```
login sign up API/
├── pom.xml
├── README.md
├── src/main/
│   ├── java/com/example/authapi/
│   │   ├── AuthApiApplication.java
│   │   ├── config/
│   │   │   ├── ApplicationConfig.java
│   │   │   ├── CorsConfig.java          # CORS configuration
│   │   │   ├── OpenApiConfig.java       # Swagger/OpenAPI configuration
│   │   │   └── SecurityConfig.java
│   │   ├── controller/
│   │   │   └── AuthController.java
│   │   ├── dto/
│   │   │   ├── AuthResponse.java
│   │   │   ├── LoginRequest.java
│   │   │   └── RegisterRequest.java
│   │   ├── entity/
│   │   │   ├── Role.java
│   │   │   └── User.java
│   │   ├── exception/
│   │   │   ├── EmailAlreadyExistsException.java
│   │   │   ├── GlobalExceptionHandler.java
│   │   │   └── InvalidCredentialsException.java
│   │   ├── repository/
│   │   │   └── UserRepository.java
│   │   ├── security/
│   │   │   ├── JwtAuthenticationFilter.java
│   │   │   └── JwtService.java
│   │   └── service/
│   │       └── AuthService.java
│   └── resources/
│       ├── application.properties
│       └── db/migration/
│           └── V1__create_users_table.sql
```

## CORS Configuration (Angular / Frontend)

This API is configured to accept requests from frontend applications. By default, the following origins are allowed:

- `http://localhost:4200` (Angular default)
- `http://localhost:3000` (React/other frameworks)
- `http://localhost:8080`

### Adding Custom Origins

To add your own frontend URL, edit `src/main/java/com/example/authapi/config/CorsConfig.java`:

```java
configuration.setAllowedOrigins(Arrays.asList(
    "http://localhost:4200",
    "http://localhost:3000",
    "http://your-frontend-url.com"  // Add your URL here
));
```

### Angular HTTP Client Example

```typescript
// auth.service.ts
import { HttpClient } from '@angular/common/http';

@Injectable({ providedIn: 'root' })
export class AuthService {
  private apiUrl = 'http://localhost:8080/api/auth';

  constructor(private http: HttpClient) {}

  register(user: any) {
    return this.http.post(`${this.apiUrl}/register`, user);
  }

  login(credentials: any) {
    return this.http.post(`${this.apiUrl}/login`, credentials);
  }
}
```

### Angular HTTP Interceptor for JWT

```typescript
// auth.interceptor.ts
import { HttpInterceptorFn } from '@angular/common/http';

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const token = localStorage.getItem('token');

  if (token) {
    req = req.clone({
      setHeaders: { Authorization: `Bearer ${token}` }
    });
  }

  return next(req);
};
```

## Security Features

- **BCrypt Password Hashing**: Passwords are hashed using BCrypt (strength 10)
- **JWT Authentication**: Stateless token-based authentication
- **Input Validation**: Email format and password length validation
- **SQL Injection Prevention**: Uses JPA parameterized queries
- **CORS Protection**: Configured allowed origins for frontend access
