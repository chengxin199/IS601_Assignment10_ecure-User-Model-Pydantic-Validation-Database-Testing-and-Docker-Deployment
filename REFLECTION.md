# 📝 Reflection Document - Module 10

## Project Overview

This project involved building a secure user authentication system with SQLAlchemy models, Pydantic validation, comprehensive testing, and Docker deployment. The main objectives were to implement password hashing, user registration/authentication, and deploy the application using Docker with a PostgreSQL database.

---

## Key Experiences

### 1. SQLAlchemy User Model Development

**What I Learned:**
- Implementing unique constraints on database columns (`username` and `email`)
- Using SQLAlchemy's declarative base for defining ORM models
- Working with UUID primary keys instead of traditional auto-incrementing integers
- Managing timestamps (`created_at`, `updated_at`) with automatic updates

**Challenges:**
- Initially struggled with understanding the difference between `flush()` and `commit()` in SQLAlchemy sessions
- Had to carefully handle database transactions to prevent partial commits when validation fails

**Solution:**
- Studied SQLAlchemy's session management documentation
- Implemented proper rollback mechanisms in the `User.register()` method to handle validation errors gracefully

---

### 2. Password Hashing and Security

**What I Learned:**
- Implementing bcrypt password hashing using `passlib.CryptContext`
- The importance of never storing plain-text passwords
- Creating separate methods for hashing (`hash_password()`) and verification (`verify_password()`)
- JWT token generation and verification for authentication

**Challenges:**
- Understanding the difference between password hashing and encryption
- Initially confused about where to hash the password (in the model vs. in the endpoint)

**Solution:**
- Implemented a static `hash_password()` method in the User model that can be called before saving
- Used the instance method `verify_password()` for authentication checks
- Kept password hashing logic centralized in the model for consistency

---

### 3. Pydantic Schema Validation

**What I Learned:**
- Creating separate schemas for different use cases (`UserCreate`, `UserResponse`)
- Using Pydantic's field validators for password complexity requirements
- The `model_validator` decorator for complex validation logic
- Excluding sensitive fields (like `password_hash`) from response schemas

**Challenges:**
- Implementing password validation that checks for uppercase, lowercase, and digits
- Understanding the difference between `@field_validator` and `@model_validator`

**Solution:**
- Created a `PasswordMixin` class with a `model_validator` that checks password requirements
- Implemented clear error messages for each validation failure
- Used inheritance to compose schemas (`UserCreate` inherits from `UserBase` and `PasswordMixin`)

---

### 4. Integration Testing with PostgreSQL

**What I Learned:**
- Setting up pytest fixtures for database session management
- Using Docker Compose to provide a test database
- Writing tests that verify unique constraints actually work
- The importance of database cleanup between tests

**Challenges:**
- **PostgreSQL Version Compatibility Issue**: Encountered an error with PostgreSQL 18+ requiring a different volume mount structure
  - Error: `"in 18+, these Docker images are configured to store database data in a format which is compatible with pg_ctlcluster"`
  
**Solution:**
- Added `PGDATA: /var/lib/postgresql/data/pgdata` environment variable to the docker-compose.yml
- This tells PostgreSQL 18+ to use a subdirectory for data storage, which is the new recommended approach
- Removed old volumes with `docker compose down -v` to start fresh

**Testing Strategy:**
- Created fixtures in `conftest.py` for:
  - `db_session`: Provides a clean database session for each test
  - `test_user`: Creates a sample user for testing
  - `seed_users`: Populates the database with multiple users
  - `fake_user_data`: Generates random user data with Faker
- Implemented `--preserve-db` flag for debugging database state after tests

---

### 5. Docker and Docker Compose

**What I Learned:**
- Building multi-stage Dockerfiles for Python applications
- Using Docker Compose to orchestrate multiple services (web app, database, pgAdmin)
- Volume management for persistent database storage
- Health checks for ensuring database is ready before starting the app

**Challenges:**
- **Docker Container Name Conflicts**: Repeatedly got errors about container names already in use
  - `Error: The container name "/fastapi_calculator" is already in use`
  
**Solution:**
- Used `docker rm -f <container-name>` to force remove old containers
- Eventually used `docker compose down` before starting new containers
- Learned to check running containers with `docker ps -a`

- **WSL 2 Docker Integration**: Initially Docker commands weren't available in WSL
  
**Solution:**
- Enabled WSL 2 integration in Docker Desktop settings
- Restarted Docker Desktop to apply changes

---

### 6. Docker Hub Deployment

**What I Learned:**
- Building Docker images with proper tagging conventions
- Pushing images to Docker Hub with multiple tags (`latest`, `v1.0`)
- The importance of semantic versioning for Docker images

**Process:**
```bash
# Build the image
docker build -t chengxin199/is601_module10:latest .

# Tag with version
docker tag chengxin199/is601_module10:latest chengxin199/is601_module10:v1.0

# Push both tags
docker push chengxin199/is601_module10:latest
docker push chengxin199/is601_module10:v1.0
```

**Result:**
- Successfully published to: https://hub.docker.com/r/chengxin199/is601_module10

---

### 7. CI/CD with GitHub Actions

**What I Learned:**
- Setting up PostgreSQL as a service in GitHub Actions
- Running tests in CI/CD pipeline
- Implementing security scanning with Trivy
- Automated Docker image building and pushing on successful tests

**Configuration Highlights:**
- PostgreSQL service container for integration tests
- Separate jobs for testing, security scanning, and deployment
- Conditional deployment only on main branch
- Multi-platform image builds (linux/amd64, linux/arm64)

---

## Most Significant Challenges

### Challenge 1: Database Transaction Management

**Problem:** When registering a user with invalid data, sometimes the database would be left in an inconsistent state.

**Solution:** 
- Implemented proper exception handling in `User.register()`
- Used `db.flush()` instead of `db.commit()` during validation
- Added explicit rollback in error cases

### Challenge 2: PostgreSQL 18+ Compatibility

**Problem:** Docker Compose was failing with PostgreSQL 18+ due to volume mount structure changes.

**Impact:** Couldn't start the database container, blocking all integration tests.

**Solution:**
- Research led to understanding PostgreSQL 18's new data directory structure
- Added `PGDATA` environment variable to specify subdirectory
- Cleaned all old volumes with `docker compose down -v`

### Challenge 3: Test Isolation

**Problem:** Tests were interfering with each other due to shared database state.

**Solution:**
- Implemented `conftest.py` with session-scoped fixtures
- Added table truncation after each test
- Created `--preserve-db` flag for debugging without affecting normal test runs

---

## Key Takeaways

1. **Security First**: Never store plain-text passwords; always use proper hashing algorithms like bcrypt

2. **Validation Layers**: Implement validation at multiple levels:
   - Database level (constraints)
   - Application level (Pydantic schemas)
   - Business logic level (custom validators)

3. **Testing is Essential**: Integration tests with real databases catch issues that unit tests miss

4. **Docker Best Practices**:
   - Always version your images
   - Use health checks for dependent services
   - Clean up old containers and volumes regularly

5. **Documentation Matters**: Clear README with setup instructions and Docker Hub links makes deployment easier

6. **Version Compatibility**: Always check documentation for breaking changes in major version upgrades (like PostgreSQL 18+)

---

## Skills Developed

- ✅ SQLAlchemy ORM modeling and relationships
- ✅ Password hashing with bcrypt
- ✅ JWT token-based authentication
- ✅ Pydantic schema validation with custom validators
- ✅ pytest fixtures and integration testing
- ✅ Docker Compose orchestration
- ✅ Docker Hub image publishing
- ✅ GitHub Actions CI/CD pipeline
- ✅ PostgreSQL database administration
- ✅ Debugging container and networking issues

---

## Future Improvements

1. **Email Verification**: Add email verification flow for user registration
2. **Password Reset**: Implement forgot password functionality with secure tokens
3. **Rate Limiting**: Add rate limiting to prevent brute force attacks
4. **Refresh Tokens**: Implement refresh token mechanism for better security
5. **Logging**: Enhanced logging for security events and errors
6. **Monitoring**: Add application monitoring with Prometheus/Grafana
7. **API Documentation**: Expand OpenAPI documentation with more examples

---

## Conclusion

This project provided hands-on experience with building a production-ready authentication system. The challenges faced, especially with Docker and PostgreSQL compatibility, taught valuable lessons about debugging and reading documentation carefully. The comprehensive testing approach ensures that the system is reliable and maintainable. The successful deployment to Docker Hub demonstrates the complete DevOps lifecycle from development to deployment.

---

**Date:** November 17, 2025  
**Author:** Cheng Xin  
**Course:** IS601  
**Module:** 10 - Secure User Model with Pydantic Validation
