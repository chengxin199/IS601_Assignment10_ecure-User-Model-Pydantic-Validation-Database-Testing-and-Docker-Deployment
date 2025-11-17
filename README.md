# 📦 Module 10 - Secure User Model with Pydantic Validation, Database Testing, and Docker Deployment

[![Docker Hub](https://img.shields.io/badge/Docker%20Hub-chengxin199%2Fis601__module10-blue?logo=docker)](https://hub.docker.com/r/chengxin199/is601_module10)

## 🐳 Docker Hub Repository

**Pull the latest image:**
```bash
docker pull chengxin199/is601_module10:latest
```

**Available tags:** `latest`, `v1.0`

**Repository Link:** [https://hub.docker.com/r/chengxin199/is601_module10](https://hub.docker.com/r/chengxin199/is601_module10)

## 📖 Additional Documentation

- **[Reflection Document](REFLECTION.md)** - Key experiences, challenges, and lessons learned during development

---

# 📦 Project Setup

---

# 🧩 1. Install Homebrew (Mac Only)

> Skip this step if you're on Windows.

Homebrew is a package manager for macOS.  
You’ll use it to easily install Git, Python, Docker, etc.

**Install Homebrew:**

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

**Verify Homebrew:**

```bash
brew --version
```

If you see a version number, you're good to go.

---

# 🧩 2. Install and Configure Git

## Install Git

- **MacOS (using Homebrew)**

```bash
brew install git
```

- **Windows**

Download and install [Git for Windows](https://git-scm.com/download/win).  
Accept the default options during installation.

**Verify Git:**

```bash
git --version
```

---

## Configure Git Globals

Set your name and email so Git tracks your commits properly:

```bash
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"
```

Confirm the settings:

```bash
git config --list
```

---

## Generate SSH Keys and Connect to GitHub

> Only do this once per machine.

1. Generate a new SSH key:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

(Press Enter at all prompts.)

2. Start the SSH agent:

```bash
eval "$(ssh-agent -s)"
```

3. Add the SSH private key to the agent:

```bash
ssh-add ~/.ssh/id_ed25519
```

4. Copy your SSH public key:

- **Mac/Linux:**

```bash
cat ~/.ssh/id_ed25519.pub | pbcopy
```

- **Windows (Git Bash):**

```bash
cat ~/.ssh/id_ed25519.pub | clip
```

5. Add the key to your GitHub account:
   - Go to [GitHub SSH Settings](https://github.com/settings/keys)
   - Click **New SSH Key**, paste the key, save.

6. Test the connection:

```bash
ssh -T git@github.com
```

You should see a success message.

---

# 🧩 3. Clone the Repository

Now you can safely clone the course project:

```bash
git clone <repository-url>
cd <repository-directory>
```

---

# 🛠️ 4. Install Python 3.10+

## Install Python

- **MacOS (Homebrew)**

```bash
brew install python
```

- **Windows**

Download and install [Python for Windows](https://www.python.org/downloads/).  
✅ Make sure you **check the box** `Add Python to PATH` during setup.

**Verify Python:**

```bash
python3 --version
```
or
```bash
python --version
```

---

## Create and Activate a Virtual Environment

(Optional but recommended)

```bash
python3 -m venv venv
source venv/bin/activate   # Mac/Linux
venv\Scripts\activate.bat  # Windows
```

### Install Required Packages

```bash
pip install -r requirements.txt
```

---

# 🐳 5. (Optional) Docker Setup

> Skip if Docker isn't used in this module.

## Install Docker

- [Install Docker Desktop for Mac](https://www.docker.com/products/docker-desktop/)
- [Install Docker Desktop for Windows](https://www.docker.com/products/docker-desktop/)

## Build Docker Image

```bash
docker build -t <image-name> .
```

## Run Docker Container

```bash
docker run -it --rm <image-name>
```

---

# 🚀 6. Running the Project

## Local Development with Docker Compose

Start all services (FastAPI app, PostgreSQL, pgAdmin):

```bash
docker compose up -d
```

The application will be available at:
- **API**: http://localhost:8000
- **API Documentation (Swagger)**: http://localhost:8000/docs
- **pgAdmin**: http://localhost:5050
  - Email: `admin@example.com`
  - Password: `admin`

Stop all services:

```bash
docker compose down
```

Stop and remove volumes (clean database):

```bash
docker compose down -v
```

## Running Without Docker

```bash
source venv/bin/activate  # Activate virtual environment
python main.py
```

---

# 🧪 7. Running Tests Locally

This project includes comprehensive unit, integration, and end-to-end tests covering user models, authentication, password hashing, and database operations.

## Prerequisites

1. **Activate virtual environment**:
   ```bash
   source venv/bin/activate   # Mac/Linux
   venv\Scripts\activate.bat  # Windows
   ```

2. **Start Docker services** (required for integration tests):
   ```bash
   docker compose up -d
   ```

## Run All Tests

```bash
pytest
```

## Run Specific Test Suites

- **Unit Tests Only**:
  ```bash
  pytest tests/unit/ -v
  ```

- **Integration Tests Only** (requires PostgreSQL):
  ```bash
  pytest tests/integration/ -v
  ```

- **End-to-End Tests**:
  ```bash
  pytest tests/e2e/ -v
  ```

## Run Tests with Coverage

```bash
pytest --cov=app --cov-report=html
```

View the coverage report by opening `htmlcov/index.html` in your browser.

## Run Specific Test Files

```bash
# Test user model and database operations
pytest tests/integration/test_user.py -v

# Test authentication and password hashing
pytest tests/integration/test_user_auth.py -v

# Test Pydantic schema validation
pytest tests/integration/test_schema_base.py -v
```

## Additional Test Options

- **Preserve Database After Tests** (for inspection):
  ```bash
  pytest tests/integration/test_user.py --preserve-db
  ```

- **Show detailed output with print statements**:
  ```bash
  pytest tests/integration/ -v -s
  ```

- **Run tests matching a keyword**:
  ```bash
  pytest -k "password" -v
  ```

- **Stop after first failure**:
  ```bash
  pytest --maxfail=1
  ```

## What's Being Tested

- ✅ **User Model**: SQLAlchemy model with unique constraints on username and email
- ✅ **Password Hashing**: bcrypt password hashing and verification
- ✅ **User Registration**: Creating users with validation
- ✅ **Authentication**: User login with JWT token generation
- ✅ **Pydantic Schemas**: UserCreate and UserRead validation
- ✅ **Database Operations**: CRUD operations, transactions, and rollbacks
- ✅ **Uniqueness Constraints**: Testing duplicate email/username prevention
- ✅ **Invalid Data Handling**: Testing invalid emails, short passwords, etc.

---

# 🐳 8. Docker Hub Repository

The Docker image for this project is published to Docker Hub:

**Repository**: [chengxin199/is601_module10](https://hub.docker.com/r/chengxin199/is601_module10)

## Pull and Run the Image

```bash
# Pull the latest image
docker pull chengxin199/is601_module10:latest

# Run the container
docker run -p 8000:8000 chengxin199/is601_module10:latest
```

## Available Tags

- `latest` - The most recent build
- `v1.0` - Version 1.0 release

## Build and Push Your Own Image

```bash
# Build the image
docker build -t chengxin199/is601_module10:latest .

# Tag with version
docker tag chengxin199/is601_module10:latest chengxin199/is601_module10:v1.0

# Login to Docker Hub
docker login

# Push to Docker Hub
docker push chengxin199/is601_module10:latest
docker push chengxin199/is601_module10:v1.0
```

---

# 📝 9. Submission Instructions

After finishing your work:

```bash
git add .
git commit -m "Complete Module X"
git push origin main
```

Then submit the GitHub repository link as instructed.

---

# 🔥 Useful Commands Cheat Sheet

| Action                         | Command                                          |
| ------------------------------- | ------------------------------------------------ |
| Install Homebrew (Mac)          | `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"` |
| Install Git                     | `brew install git` or Git for Windows installer |
| Configure Git Global Username  | `git config --global user.name "Your Name"`      |
| Configure Git Global Email     | `git config --global user.email "you@example.com"` |
| Clone Repository                | `git clone <repo-url>`                          |
| Create Virtual Environment     | `python3 -m venv venv`                           |
| Activate Virtual Environment   | `source venv/bin/activate` / `venv\Scripts\activate.bat` |
| Install Python Packages        | `pip install -r requirements.txt`               |
| Build Docker Image              | `docker build -t <image-name> .`                |
| Run Docker Container            | `docker run -it --rm <image-name>`               |
| Push Code to GitHub             | `git add . && git commit -m "message" && git push` |

---

# 📋 Notes

- Install **Homebrew** first on Mac.
- Install and configure **Git** and **SSH** before cloning.
- Use **Python 3.10+** and **virtual environments** for Python projects.
- **Docker** is optional depending on the project.

---

# 📎 Quick Links

- [Homebrew](https://brew.sh/)
- [Git Downloads](https://git-scm.com/downloads)
- [Python Downloads](https://www.python.org/downloads/)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [GitHub SSH Setup Guide](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
