# DevOps Practicals Guide
### Student: Atharva Ahirrao | GitHub: https://github.com/Atharvaahirrao/pract5

---

# PRACTICAL 3 — Jenkins Declarative Pipeline

## Aim
Create and execute a Jenkins Declarative Pipeline connected to a GitHub repository.

## Steps

### 1. Open Jenkins
- Open browser → `http://localhost:8080`
- Login with username and password

### 2. Create New Item
- Click **New Item**
- Name: `prac3`
- Select **Pipeline** → Click **OK**

### 3. Pipeline Script
- Scroll to **Pipeline** section
- Definition: **Pipeline script**
- Paste the code below

```groovy
pipeline {
    agent any
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'master',
                url: 'https://github.com/Atharvaahirrao/pract5'
            }
        }
        stage('Build') {
            steps {
                echo "Running Build Steps..."
                bat 'dir'
            }
        }
        stage('Unit Test') {
            steps {
                echo "Running Unit Test..."
                bat 'dir'
            }
        }
        stage('Integration Test') {
            steps {
                echo "Running Integration Test..."
                bat 'echo Test passed successfully'
            }
        }
    }
    post {
        success {
            echo "Pipeline completed successfully"
        }
        failure {
            echo "Pipeline failed"
        }
    }
}
```

### 4. Save and Build
- Click **Save**
- Click **Build Now**
- Wait for build to finish

### 5. Verify
- Click build `#1` → Click **Pipeline Overview** → All stages GREEN ✓
- Click **Console Output** → Last line: `Finished: SUCCESS`

---

# PRACTICAL 4A — Pipeline with File Archiving + Scheduled Trigger

## Aim
Configure Jenkins Pipeline to run automatically every minute and archive a file.

## Steps

### 1. Create New Item
- Click **New Item**
- Name: `prac4a`
- Select **Pipeline** → Click **OK**

### 2. Set Build Trigger
- Scroll to **General**
- Check **Build periodically**
- Schedule:

```
* * * * *
```

### 3. Pipeline Script

```groovy
pipeline {
    agent any
    stages {
        stage('Create File') {
            steps {
                bat 'echo Hello Jenkins Pipeline > demo.txt'
            }
        }
        stage('Archive File') {
            steps {
                archiveArtifacts 'demo.txt'
            }
        }
    }
}
```

### 4. Save and Verify
- Click **Save**
- Wait 1 minute → Build triggers automatically
- Console Output first line: `Started by timer`
- Project page shows **Last Successful Artifacts** → `demo.txt`

## Cron Reference
| Schedule | Meaning |
|----------|---------|
| `* * * * *` | Every minute |
| `H/5 * * * *` | Every 5 minutes |
| `H * * * *` | Every hour |
| `0 10 * * *` | Daily at 10 AM |

---

# PRACTICAL 4B — Freestyle Job with Auto Scheduling

## Aim
Configure a Freestyle Jenkins job to run automatically every minute.

## Steps

### 1. Create New Item
- Click **New Item**
- Name: `prac4b`
- Select **Freestyle project** → Click **OK**

### 2. Set Trigger
- Scroll to **Build Triggers**
- Check **Build periodically**
- Schedule: `* * * * *`

### 3. Build Step
- Scroll to **Build Steps**
- Click **Add build step**
- Select **Execute Windows batch command**
- Enter:

```batch
echo This job is running automatically
date /T
time /T
```

### 4. Save and Verify
- Click **Save** → Wait 1 minute
- Console Output shows:

```
Started by timer
This job is running automatically
Sun 03/22/2026
09:18 PM
Finished: SUCCESS
```

---

# PRACTICAL 4C — Parameterized Scheduling

## Aim
Create a Jenkins Pipeline with a string parameter that runs on schedule and saves output as artifact.

## Steps

### 1. Create New Item
- Click **New Item**
- Name: `prac4c`
- Select **Pipeline** → Click **OK**

### 2. Pipeline Script

```groovy
pipeline {
    agent any

    parameters {
        string(name: 'USERNAME',
               defaultValue: 'Student',
               description: 'Enter your name')
    }

    triggers {
        cron('H/1 * * * *')
    }

    stages {
        stage('Greeting') {
            steps {
                echo "Hello, ${params.USERNAME}!"
                bat "echo Hello ${params.USERNAME}, this is your scheduled Jenkins job! > message.txt"
            }
        }
        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'message.txt'
            }
        }
    }

    post {
        success {
            echo "Build completed successfully!"
        }
    }
}
```

### 3. Save and Build
- Click **Save**
- Click **Build with Parameters**
- Enter your name or leave as `Student`
- Click **Build**

### 4. Verify
- Console Output: `Hello, Student!`
- Last Successful Artifacts → click `message.txt`
- File shows: `Hello Student, this is your scheduled Jenkins job!`

---

# PRACTICAL 5A — Maven Project Build via Jenkins

## Aim
Configure Jenkins to automatically build a Java Maven project — download dependencies, compile, test, generate JAR.

---

## PART 1 — Fix pom.xml in NetBeans

### 1. Open pom.xml
- Open NetBeans → Expand `mavenproject11`
- Double click `pom.xml`

### 2. Change Java Version
Find:
```xml
<maven.compiler.release>23</maven.compiler.release>
```
Change to:
```xml
<maven.compiler.release>11</maven.compiler.release>
```
- Press `Ctrl + S` to save

---

## PART 2 — Push to GitHub

### 3. Git Bash Commands

```bash
cd "C:\Users\athar\OneDrive\Documents\NetBeansProjects\mavenproject11"
git add .
git commit -m "fix java version to 11"
git push origin master
```

---

## PART 3 — Configure Jenkins

### 4. Install Maven Plugin
- Jenkins → **Manage Jenkins** → **Plugins**
- Click **Available plugins**
- Search: `Maven Integration`
- Check it → Click **Install**

### 5. Configure Maven Tool
- **Manage Jenkins** → **Tools**
- Scroll to **Maven installations** → Click **Add Maven**
- Name: `maven`
- Check **Install automatically**
- Version: `3.9.11`
- Click **Save**

### 6. Create Maven Job
- Click **New Item**
- Name: `prac5`
- Select **Maven project** → Click **OK**

### 7. Configure SCM
- Source Code Management → Select **Git**
- Repository URL: `https://github.com/Atharvaahirrao/pract5`
- Credentials: `--none--`
- Branch Specifier: `*/master`

### 8. Configure Build
- Scroll to **Build** section
- Root POM: `pom.xml`
- Goals and options: `clean test package`

### 9. Post Build Actions
- Click **Add post-build action**
- Select **Archive the artifacts**
- Files to archive: `target/*.jar`

### 10. Save and Build
- Click **Save** → Click **Build Now**
- Expected Console Output:

```
[INFO] Building mavenproject11 1.0-SNAPSHOT
[INFO] BUILD SUCCESS
Archiving artifacts
Finished: SUCCESS
```

---

# PRACTICAL 5B — Ant Project Build via Jenkins

## Aim
Configure Jenkins to automatically build a Java Ant project — compile, test, generate JAR artifact.

---

## PART 1 — Create Ant Project in NetBeans

### 1. Create New Project
- File → New Project
- Categories: **Java with Ant**
- Projects: **Java Application**
- Name: `JavaApplication1` → Click **Finish**

> NetBeans auto-generates `build.xml` — DO NOT delete it

---

## PART 2 — Push to GitHub

### 2. Create New GitHub Repo
- Go to https://github.com → Click **+** → **New repository**
- Name: `ant-project`
- Keep Public — do NOT add README
- Click **Create repository**

### 3. Git Bash Commands

```bash
cd "C:\Users\athar\OneDrive\Documents\NetBeansProjects\JavaApplication1"
git init
git add .
git commit -m "java ant commit"
git remote add origin https://github.com/Atharvaahirrao/ant-project
git push origin -u master
```

---

## PART 3 — Configure Jenkins

### 4. Configure Ant Tool
- **Manage Jenkins** → **Tools**
- Scroll to **Ant installations** → Click **Add Ant**
- Name: `ant`
- Check **Install automatically**
- Version: `1.10.15`
- Click **Save**

### 5. Create Freestyle Job
- New Item → Name: `prac5b`
- Select **Freestyle project** → Click **OK**

### 6. Configure SCM
- Source Code Management → **Git**
- URL: `https://github.com/Atharvaahirrao/ant-project`
- Branch: `*/master`

### 7. Build Step
- Build Steps → **Add build step** → **Invoke Ant**
- Ant Version: `ant`
- Targets: `clean jar`

### 8. Post Build
- Add post-build action → **Archive the artifacts**
- Files: `dist/*.jar`
- Click **Save** → Click **Build Now**

> Expected: `Finished: SUCCESS` with JAR archived

---

# PRACTICAL 6A — Docker Basic Commands

## Aim
Understand Docker architecture and run basic commands to manage images and containers.

## Commands

### 1. Check Version
```cmd
docker --version
```

### 2. System Info
```cmd
docker info
```

### 3. Pull and Run Hello World
```cmd
docker pull hello-world
docker run hello-world
```

### 4. Run Apache HTTP Server
```cmd
docker run -d -p 8082:80 httpd
```
- Open browser → `http://localhost:8082` → **"It works!"**

### 5. Run Nginx
```cmd
docker run -d -p 8083:80 nginx
```
- Open browser → `http://localhost:8083` → **"Welcome to nginx!"**

### 6. Run Python Interactively
```cmd
docker run -it python
```
- Python shell opens → type `1+2` → output `3`
- Type `exit()` to come out

### 7. Manage Containers and Images
```cmd
docker ps           # Running containers
docker ps -a        # All containers
docker images       # All images
docker stop <id>    # Stop container
docker rm <id>      # Remove container
```

## Flags Reference
| Flag | Meaning |
|------|---------|
| `-d` | Detached — runs in background |
| `-p 8082:80` | Map host port 8082 to container port 80 |
| `-it` | Interactive terminal |

---

# PRACTICAL 6B — Dockerfile Static Website

## Aim
Build a custom Docker image using Dockerfile to serve a static HTML page using Apache.

## Steps

### 1. Create Project Folder
```cmd
mkdir C:\Users\athar\docker-web1
cd C:\Users\athar\docker-web1
```

### 2. Create `index.html`
> Open Notepad → paste code → Save as `index.html` in docker-web1 folder

```html
<!DOCTYPE html>
<html>
<head>
    <title>Dockerfile website</title>
</head>
<body>
    <h1>Hello from Dockerfile!</h1>
    <p>This is hosted using Dockerfile</p>
</body>
</html>
```

### 3. Create `Dockerfile`
> Open Notepad → paste code → Save as `Dockerfile`
> ⚠️ NO extension! In Save dialog: File name = `Dockerfile`, Save as type = **All Files**

```dockerfile
FROM httpd:latest
COPY index.html /usr/local/apache2/htdocs/
EXPOSE 80
```

### 4. Build Image
```cmd
docker build -t myimage .
```

### 5. Run Container
```cmd
docker run -d -p 8085:80 myimage
```

### 6. Test
- Open browser → `http://localhost:8085`
- You see: **"Hello from Dockerfile!"**

## Dockerfile Explanation
| Line | Meaning |
|------|---------|
| `FROM httpd:latest` | Use official Apache image as base |
| `COPY index.html /usr/local/apache2/htdocs/` | Copy HTML into Apache web directory |
| `EXPOSE 80` | Document that app uses port 80 |

---

# PRACTICAL 6C — Dockerfile PHP Dynamic App

## Aim
Build a Docker image with PHP + Apache to handle a form and generate dynamic response.

## Steps

### 1. Create Project Folder
```cmd
mkdir C:\Users\athar\docker-web2
cd C:\Users\athar\docker-web2
```

### 2. Create `index.html`
> Open Notepad → paste code → Save as `index.html` in docker-web2

```html
<!DOCTYPE html>
<html>
<head>
    <title>Dockerfile form</title>
</head>
<body>
    <h2>Simple form (Docker)</h2>
    <form action="submit.php" method="post">
        Name: <input type="text" name="username" required>
        <br><br>
        <input type="submit" value="Submit">
    </form>
</body>
</html>
```

### 3. Create `submit.php`
> Open Notepad → paste code → Save as `submit.php` in docker-web2

```php
<?php
$name = $_POST['username'];
echo "<h1>Hello, $name!</h1>";
echo "<p>This response is generated dynamically using PHP inside Docker.</p>";
?>
```

### 4. Create `Dockerfile`
> Open Notepad → paste code → Save as `Dockerfile` (All Files, no extension)

```dockerfile
FROM php:8.2-apache
COPY . /var/www/html/
EXPOSE 80
```

### 5. Build and Run
```cmd
docker build -t docker-web2 .
docker run -d -p 8088:80 docker-web2
```

### 6. Test
- Open browser → `http://localhost:8088`
- You see the form → Enter your name → Click Submit
- PHP responds: **"Hello, Atharva!"**

## Dockerfile Explanation
| Line | Meaning |
|------|---------|
| `FROM php:8.2-apache` | PHP 8.2 with Apache built-in |
| `COPY . /var/www/html/` | Copy ALL files to web root |
| `EXPOSE 80` | Port 80 for HTTP |

---

# QUICK REFERENCE

## Git Commands
```bash
git init
git add .
git commit -m "message"
git remote add origin <url>
git push origin master
git pull origin master --allow-unrelated-histories
git push origin master --force
git branch
```

## Docker Commands
```cmd
docker --version
docker info
docker pull <image>
docker run hello-world
docker run -d -p 8082:80 httpd
docker run -d -p 8083:80 nginx
docker run -it python
docker ps
docker ps -a
docker images
docker build -t myimage .
docker run -d -p 8085:80 myimage
docker stop <container-id>
docker rm <container-id>
```

## Jenkins Pipeline Template
```groovy
pipeline {
    agent any
    stages {
        stage('Stage Name') {
            steps {
                // your commands here
            }
        }
    }
    post {
        success { echo "Done" }
        failure { echo "Failed" }
    }
}
```

---

## Viva Key Points

| Topic | Key Point |
|-------|-----------|
| Jenkins | CI/CD automation server — runs on localhost:8080 |
| Freestyle Project | Simple job, manual steps, no code needed |
| Declarative Pipeline | Code-based CI/CD using Groovy `pipeline { }` block |
| Cron `* * * * *` | Every minute |
| Maven | Java build tool — uses `pom.xml` |
| Ant | Java build tool — uses `build.xml` |
| Docker Image | Blueprint/template (like a class) |
| Docker Container | Running instance of image (like an object) |
| Dockerfile | Script to build a custom image |
| `-d` flag | Run container in background |
| `-p 8080:80` | Map host port to container port |
| `-it` flag | Interactive terminal inside container |
| `FROM` | Base image to start from |
| `COPY` | Copy files into image |
| `EXPOSE` | Document which port app uses |
