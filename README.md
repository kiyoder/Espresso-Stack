# Espresso-Stack — Monorepo Template (Web + Mobile)

Purpose
-------
This repository is a template/boilerplate meant to be used as the starting point for future web + mobile projects in a single monorepo. It contains an example Java Spring Boot backend and an Expo (React Native) frontend (with NativeWind styling) and demonstrates an Nx workspace integration. Note: the Nx integration was a "nice to have" demonstration — it can be ignored if you'd rather not use Nx.

Quick overview
--------------
- Backend: Java Spring Boot (Maven), Java 21, PostgreSQL, Spring Data JPA, Spring Security, Lombok
- Frontend: Expo (React Native), NativeWind (Tailwind-style styling), web support via react-native-web
- Monorepo: Nx used to manage project tasks and targets (optional)
- Purpose: template + step-by-step recreation notes in this README

Repository Layout Structure
---------------------------
- frontend/
    - my-expo-app/      (Expo app created with NativeWind)
- backend/
    - springboot/       (Spring Boot project)

Installation / Setup Instructions
-----------------------------------
Follow these steps to configure and run the template locally.

1. Clone the repo
```bash
git clone https://github.com/kiyoder/Espresso-Stack.git
cd Espresso-Stack
```

2. Configure the Spring Boot application (required)
- Open `backend/springboot/src/main/resources/application.yaml`
- Edit the datasource section to point to your PostgreSQL instance. Example:
```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/postgresql_db
    username: YOUR_DB_USER
    password: YOUR_DB_PASSWORD
```
- Ensure the database (`postgresql_db` in the example) exists in PostgreSQL. You can create it using pgAdmin or psql:
    - In pgAdmin: connect to your server → right-click Databases → Create → Database → give it the same name used in `application.yaml`.

3. Install frontend dependencies
```bash
cd frontend/my-expo-app
npm install
# For web support
npx expo install react-dom react-native-web
cd ../../
```


5. Run the apps (two options)
- Recommended (run from root in separate terminals):
```bash
# Terminal 1 (starts the Spring Boot backend)
npm run backend

# Terminal 2 (starts the Expo frontend)
npm run frontend
```
- Manual / Alternative:
    - Backend (Unix/macOS):
      ```bash
      cd backend/springboot
      ./mvnw spring-boot:run
      ```
    - Backend (Windows):
      ```bash
      cd backend/springboot
      mvnw.cmd spring-boot:run
      ```
    - Frontend:
      ```bash
      cd frontend/my-expo-app
      npx expo start
      ```

Notes about IntelliJ (Backend)
------------------------------
- Spring Boot usually auto-detects when you import the module. If it doesn't, do the following:

1. Import the Spring Boot module
- File > Project Structure > Project Settings > Modules > Add > Import Module
- Select `backend/springboot/pom.xml` and import.

2. Set the SDK / JDK for the module
- Open `SpringbootApplication.java` in `backend/springboot/src/main/java/com/backend/springboot/SpringbootApplication.java`
- Click "Setup SDK" (top-right) or: File > Project Structure > Project Settings > Project
- Choose or download the JDK (Java 21 recommended for this template).

3. Create a Spring Boot run configuration (if IntelliJ didn't create one)
- Run > Edit Configurations > Add (+) > Spring Boot
- Name: Springboot Application
- Module: select the `springboot` module (openjdk-21 - springboot or similar)
- Main class: `com.backend.springboot.SpringbootApplication`
- Apply and Run.

Notes about Expo / Frontend in IDE
----------------------------------
- You can run the frontend directly from a terminal:
    - cd `frontend/my-expo-app` and run `npx expo start`


How I created this monorepo (repro steps)
-----------------------------------------
1. Create the monorepo root and directories
```bash
mkdir monorepo-app
cd monorepo-app
mkdir frontend backend
```

2. Initialize Nx at the repo root (optional)
```bash
npm init -y
npx nx init
# Choose "Minimum" setup when prompted, skip optional features you don't need
nx --version
# Example output:
# Local: v22.1.3
# Global: v22.1.3
```
Note: The Nx setup here is optional — it was included as a convenient way to run both frontend and backend targets from the root.

Frontend — Expo + NativeWind
----------------------------
Refer to the official Expo docs for most up-to-date install steps:
https://docs.expo.dev/tutorial/create-your-first-app/

1. Create the Expo app (example uses rn-new helper which scaffolds Expo + NativeWind)
```bash
cd frontend
# Creates new Expo App called my-expo-app with NativeWind configured
npx rn-new --nativewind
cd my-expo-app
```

2. Enable web support (if you want to run in a browser)
```bash
npx expo install react-dom react-native-web
```

3. Run the Expo app
```bash
npm start
# or via Nx (if you added Nx targets): nx serve frontend
```

NativeWind installation reference:
https://www.nativewind.dev/docs/getting-started/installation

Backend — Spring Boot (Maven)
-----------------------------
When creating the Spring Boot project use:
- Project: Maven
- Language: Java
- Spring Boot: Latest stable (e.g. 4.x)
- Group: com.backend
- Artifact: springboot
- Name: springboot
- Description: springboot backend
- Package name: com.backend.springboot
- Packaging: jar
- Configuration: YAML
- Java version: 21

Dependencies to include:
- Spring Web
- Spring Data JPA
- Spring Security
- PostgreSQL Driver
- Lombok

Sample application.yaml (replace credentials/database name as needed)
```yaml
spring:
  application:
    name: springboot
  datasource:
    # JDBC connection URL to your PostgreSQL database.
    # Modify 'localhost', port '5432', or database name 'postgresql_db' if your DB location changes.
    url: jdbc:postgresql://localhost:5432/postgresql_db

    # Database username — change if using a different DB user.
    username: YOUR_DB_USER

    # Database password — update if credentials change.
    password: YOUR_DB_PASSWORD

    # PostgreSQL JDBC driver class
    driver-class-name: org.postgresql.Driver

    jpa:
      hibernate:
        # ddl-auto controls schema generation:
        # 'update' updates the schema automatically.
        ddl-auto: update

      properties:
        hibernate:
          # Hibernate dialect for PostgreSQL
          dialect: org.hibernate.dialect.PostgreSQLDialect
```

IntelliJ tips for Spring Boot in a monorepo
-------------------------------------------
- File > Project Structure > Project Settings > Modules > Add > Import Module
    - Locate and select `backend/springboot/pom.xml`
- Open `SpringbootApplication.java` and click "Setup SDK" (top-right), then:
    - Download JDK (select Java 21 if asked) — this sets project SDK for the module
- To change SDK later: File > Project Structure > SDKs
- If IntelliJ doesn't have a Spring Boot run configuration:
    - Run > Edit Configurations > Add (+) > Spring Boot
    - Name: Springboot Application
    - Module: (select the springboot module / openjdk-21 - springboot)
    - Search and pick `com.backend.springboot.SpringbootApplication`
    - Apply

Nx targets and project config
-----------------------------
Example project names reported by `nx` in this setup:
```
frontend
backend
serve-all
```

Example Nx-style package.json entries for each subproject
(Place these as JSON objects in the Nx project definitions — below are sample `project.json`-style fragments or simply place them in top-level `package.json` mapping if you prefer.)

backend/springboot/package.json
```json
{
  "name": "backend",
  "root": "backend/springboot",
  "sourceRoot": "backend/springboot/src",
  "projectType": "application",
  "targets": {
    "build": {
      "executor": "nx:run-commands",
      "options": {
        "command": "mvnw.cmd package",
        "cwd": "backend/springboot"
      }
    },
    "serve": {
      "executor": "nx:run-commands",
      "options": {
        "command": "mvnw.cmd spring-boot:run",
        "cwd": "backend/springboot"
      }
    },
    "lint": {
      "executor": "nx:run-commands",
      "options": {
        "command": "echo 'No lint configured for backend'"
      }
    }
  }
}
```

frontend/my-expo-app/package.json
```json
{
  "name": "frontend",
  "root": "frontend/my-expo-app",
  "sourceRoot": "frontend/my-expo-app",
  "projectType": "application",
  "targets": {
    "serve": {
      "executor": "nx:run-commands",
      "options": {
        "command": "npx expo start",
        "cwd": "frontend/my-expo-app",
        "parallel": false,
        "forwardAllArgs": true
      }
    },
    "android": {
      "executor": "nx:run-commands",
      "options": {
        "command": "npx expo start --android",
        "cwd": "frontend/my-expo-app",
        "parallel": false,
        "forwardAllArgs": true
      }
    },
    "ios": {
      "executor": "nx:run-commands",
      "options": {
        "command": "npx expo start --ios",
        "cwd": "frontend/my-expo-app",
        "parallel": false,
        "forwardAllArgs": true
      }
    },
    "web": {
      "executor": "nx:run-commands",
      "options": {
        "command": "npx expo start --web",
        "cwd": "frontend/my-expo-app",
        "parallel": false,
        "forwardAllArgs": true
      }
    }
  }
}
```

Root package.json scripts
-------------------------
Add scripts at the repo root for convenience:
```json
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "frontend": "cd frontend/my-expo-app && npx expo start",
  "backend": "cd backend/springboot && mvnw.cmd spring-boot:run"
}
```
Run these in separate terminals:
- `npm run frontend`
- `npm run backend`

IntelliJ: run Expo from IDE
---------------------------
To run frontend from IntelliJ:
- Run > Edit Configurations > Add (+) > npm
- Command: run
- Script: frontend

Repeat similarly for backend (choose the `backend` script).

Notes and recommendations
-------------------------
- Nx is optional: I added Nx to demonstrate a unified command structure and to show how targets can be defined for both frontend and backend. If you don't need Nx, you can manage the two projects independently.
- Replace placeholder DB username/password and DB name with your real credentials in `application.yaml`.
- The Spring Boot example uses `ddl-auto: update` for convenience during development; consider safer options for production deployments.
- Java 21 was chosen for compatibility with Maven and current features; adjust to your team’s JDK preferences if needed.

Enjoy using this monorepo template — it's designed to let you start web and mobile development together with a consistent structure.