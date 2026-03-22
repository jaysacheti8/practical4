PRACTICAL 3 — Jenkins Declarative Pipeline
What You Need Ready

Jenkins running on localhost:8080
GitHub account
A repository on GitHub (yours is https://github.com/Atharvaahirrao/pract5)


Steps
1. Open Jenkins

Open browser
Go to http://localhost:8080
Login with your username and password

2. Create New Item

Click "New Item" on left sidebar
Enter name: prac3
Select "Pipeline"
Click OK

3. Go to Pipeline Section

Scroll down to "Pipeline" section
Definition: "Pipeline script"
Copy paste this code:

groovypipeline {
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
- Click **"Build Now"**
- Wait for build to finish

### 5. Verify
- Click on build `#1`
- Click **"Pipeline Overview"**
- All stages should be **GREEN**
- Click **"Console Output"**
- Last line should say `Finished: SUCCESS`

---

## What to Show in Practical
- Pipeline Overview with all green stages
- Console Output showing SUCCESS
- The pipeline script code

---
---

# PRACTICAL 4 — Build Trigger Scheduling

## Three Parts: 4A, 4B, 4C

---

# PRACTICAL 4A — Pipeline with File Archiving

## Steps

### 1. Create New Item
- Click **"New Item"**
- Name: `prac4a`
- Select **"Pipeline"**
- Click **OK**

### 2. Set Trigger
- Scroll to **"General"** section
- Check **"Build periodically"**
- In Schedule box type:
```
* * * * *

Jenkins will warn — that is normal, ignore it

3. Write Pipeline Script
groovypipeline {
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

### 4. Save
- Click **Save**
- Wait 1 minute
- Build will trigger automatically
- You will see `#1`, `#2` builds appearing

### 5. Verify
- Click on any build
- Click **"Console Output"**
- First line says **"Started by timer"**
- Last line says `Finished: SUCCESS`
- Go back to project page
- You will see **"Last Successful Artifacts"** showing `demo.txt`

---

# PRACTICAL 4B — Freestyle Auto Scheduling

## Steps

### 1. Create New Item
- Click **"New Item"**
- Name: `prac4b`
- Select **"Freestyle project"**
- Click **OK**

### 2. Set Trigger
- Scroll to **"Build Triggers"** section
- Check **"Build periodically"**
- Schedule:
```
* * * * *
3. Add Build Step

Scroll to "Build Steps"
Click "Add build step"
Select "Execute Windows batch command"
Type:

batchecho This job is running automatically
date /T
time /T
```

### 4. Save and Verify
- Click **Save**
- Wait 1 minute
- Build triggers automatically
- Console Output shows:
```
Started by timer
This job is running automatically
Sun 03/22/2026
09:18 PM
Finished: SUCCESS

PRACTICAL 4C — Parameterized Scheduling
Steps
1. Create New Item

Click "New Item"
Name: prac4c
Select "Pipeline"
Click OK

2. Write Pipeline Script
groovypipeline {
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

### 3. Save
- Click **Save**

### 4. First Build — Manual
- Click **"Build with Parameters"**
- USERNAME field shows — type your name or leave as `Student`
- Click **Build**

### 5. Verify
- Click on build
- Console Output shows:
```
Hello, Student!
Finished: SUCCESS

Go to project page
Click "Last Successful Artifacts"
Click message.txt
It shows: Hello Student, this is your scheduled Jenkins job!


Cron Schedule Cheat Sheet to Remember
WhatScheduleEvery minute* * * * *Every 5 minutesH/5 * * * *Every hourH * * * *Daily at 10 AM0 10 * * *


PRACTICAL 5A — Maven Project Build
What You Need Ready

NetBeans IDE installed
Maven project created (mavenproject11)
GitHub repo (https://github.com/Atharvaahirrao/pract5)
Git Bash installed


PART 1 — Prepare Project in NetBeans
1. Open NetBeans

Open NetBeans IDE
Your project mavenproject11 should already be there

2. Fix pom.xml (IMPORTANT)

Expand mavenproject11 in Projects panel
Find pom.xml — double click to open
Find this line:

xml<maven.compiler.release>23</maven.compiler.release>

Change it to:

xml<maven.compiler.release>11</maven.compiler.release>

Press Ctrl + S to save

3. Your Java Code Should Be
javapackage com.mycompany.mavenproject11;

public class Mavenproject11 {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}

PART 2 — Push to GitHub
1. Open Git Bash

Right click on Desktop
Click "Git Bash Here"

2. Run Commands One by One
bashcd "C:\Users\athar\OneDrive\Documents\NetBeansProjects\mavenproject11"
bashgit add .
bashgit commit -m "fix java version"
bashgit push origin master
```

### 3. Verify on GitHub
- Go to `https://github.com/Atharvaahirrao/pract5`
- You should see `pom.xml` and `src` folder

---

## PART 3 — Configure Jenkins

### 1. Install Maven Plugin
- Go to Jenkins → **Manage Jenkins**
- Click **Plugins**
- Click **Available plugins**
- Search: `Maven Integration`
- Check it → Click **Install**
- Wait for install to finish

### 2. Configure Maven Tool
- Go to **Manage Jenkins** → **Tools**
- Scroll to **Maven installations**
- Click **Add Maven**
- Name: `maven`
- Check **Install automatically**
- Version: `3.9.11`
- Click **Save**

### 3. Create Maven Job
- Click **"New Item"**
- Name: `prac5`
- Select **"Maven project"**
- Click **OK**

### 4. Configure SCM
- Scroll to **Source Code Management**
- Select **Git**
- Repository URL: `https://github.com/Atharvaahirrao/pract5`
- Credentials: `--none--`
- Branch: `*/master`

### 5. Configure Build
- Scroll to **Build** section
- Root POM: `pom.xml`
- Goals: `clean test package`

### 6. Post Build Actions
- Click **Add post-build action**
- Select **Archive the artifacts**
- Files to archive: `target/*.jar`

### 7. Save and Build
- Click **Save**
- Click **Build Now**

### 8. Verify
- Console Output last lines should show:
```
[INFO] BUILD SUCCESS
Archiving artifacts
Finished: SUCCESS

PART 4 — What to Show in Practical

NetBeans with mavenproject11 open
pom.xml with Java 11
GitHub repo with the files
Jenkins build showing SUCCESS
Console Output showing BUILD SUCCESS



PRACTICAL 5B — Ant Project Build
What You Need Ready

NetBeans IDE
Java Ant project created
New GitHub repo for Ant project


PART 1 — Create Ant Project in NetBeans
1. Open NetBeans

File → New Project
Categories: Java with Ant
Projects: Java Application
Name: JavaApplication1
Click Finish

2. Your Java Code
NetBeans auto-generates:
javapackage javaapplication1;

public class JavaApplication1 {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}
3. Important Files Generated

build.xml — Ant build file (auto-generated by NetBeans)
src/ — Your Java source code


PART 2 — Create GitHub Repo and Push
1. Create New Repo on GitHub

Go to https://github.com
Click + → New repository
Name: ant-project
Keep it Public
Do NOT add README
Click Create repository

2. Push from Git Bash
bashcd "C:\Users\athar\OneDrive\Documents\NetBeansProjects\JavaApplication1"
bashgit init
bashgit add .
bashgit commit -m "java ant commit"
bashgit remote add origin https://github.com/Atharvaahirrao/ant-project
bashgit push origin -u master
```

---

## PART 3 — Configure Jenkins

### 1. Configure Ant Tool
- Go to **Manage Jenkins** → **Tools**
- Scroll to **Ant installations**
- Click **Add Ant**
- Name: `ant`
- Check **Install automatically**
- Version: `1.10.15`
- Click **Save**

### 2. Create Freestyle Job
- Click **"New Item"**
- Name: `prac5b`
- Select **"Freestyle project"**
- Click **OK**

### 3. Configure SCM
- Source Code Management → **Git**
- URL: `https://github.com/Atharvaahirrao/ant-project`
- Branch: `*/master`

### 4. Build Step
- Build Steps → **Add build step**
- Select **"Invoke Ant"**
- Ant Version: `ant`
- Targets: `clean jar`

### 5. Post Build
- Add post-build action
- Archive the artifacts
- Files: `dist/*.jar`

### 6. Save and Build
- Click **Save**
- Click **Build Now**

### 7. Verify
```
Finished: SUCCESS


PRACTICAL 6A — Docker Basic Commands
What You Need Ready

Docker Desktop installed and running
Command Prompt open


Steps
1. Open Docker Desktop

Find Docker Desktop in Start Menu
Open it
Wait for it to say "Engine running" at bottom left

2. Open Command Prompt

Press Windows + R
Type cmd
Press Enter

3. Run These Commands One by One
Check version:
cmddocker --version
Output: Docker version 29.1.3
Check system info:
cmddocker info
Shows OS, memory, containers count etc.
Pull hello-world image:
cmddocker pull hello-world
Run hello-world:
cmddocker run hello-world
Output: Hello from Docker!
Run Apache HTTP Server:
cmddocker run -d -p 8082:80 httpd
Open browser → http://localhost:8082
You see: "It works!"
Run Nginx:
cmddocker run -d -p 8083:80 nginx
Open browser → http://localhost:8083
You see: "Welcome to nginx!"
Run Python interactively:
cmddocker run -it python
Python shell opens — type 1+2 → output 3
Type exit() to come out
4. Check Running Containers
cmddocker ps
5. Check All Images
cmddocker images
You will see: hello-world, httpd, nginx, python

What to Show in Practical

Docker Desktop with images listed
Command Prompt with commands run
Browser showing "It works!" for httpd
Browser showing "Welcome to nginx!"



PRACTICAL 6B — Dockerfile Static Website
Steps
1. Create Project Folder

Open Command Prompt

cmdmkdir C:\Users\athar\docker-web1
cd C:\Users\athar\docker-web1
2. Create index.html

Open Notepad
Type this:

html<!DOCTYPE html>
<html>
<head>
    <title>Dockerfile website</title>
</head>
<body>
    <h1>Hello from Dockerfile!</h1>
    <p>This is hosted using Dockerfile</p>
</body>
</html>

Save as index.html in C:\Users\athar\docker-web1

3. Create Dockerfile

Open Notepad again
Type this:

dockerfileFROM httpd:latest
COPY index.html /usr/local/apache2/htdocs/
EXPOSE 80

Save as Dockerfile (no extension, exactly this name)
In Save dialog:

File name: Dockerfile
Save as type: All Files
Click Save



4. Open Command Prompt in That Folder
cmdcd C:\Users\athar\docker-web1
5. Build the Docker Image
cmddocker build -t myimage .
Wait for it to finish — you will see layers being built
6. Run the Container
cmddocker run -d -p 8085:80 myimage
7. Open Browser

Go to http://localhost:8085
You see: "Hello from Dockerfile!"

8. Verify in Docker Desktop

Open Docker Desktop
Click Containers
You will see your container running


What to Show in Practical

The Dockerfile content
The index.html content
Command Prompt showing docker build and docker run
Browser showing "Hello from Dockerfile!"
Docker Desktop showing container running



PRACTICAL 6C — Dockerfile PHP Dynamic App
Steps
1. Create Project Folder
cmdmkdir C:\Users\athar\docker-web2
cd C:\Users\athar\docker-web2
2. Create index.html

Open Notepad
Type:

html<!DOCTYPE html>
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

Save as index.html in docker-web2 folder

3. Create submit.php

Open Notepad
Type:

php<?php
$name = $_POST['username'];
echo "<h1>Hello, $name!</h1>";
echo "<p>This response is generated dynamically using PHP inside Docker.</p>";
?>

Save as submit.php in docker-web2 folder

4. Create Dockerfile

Open Notepad
Type:

dockerfileFROM php:8.2-apache
COPY . /var/www/html/
EXPOSE 80

Save as Dockerfile (All Files, no extension)

5. Build Image
cmddocker build -t docker-web2 .
This takes longer because it downloads PHP + Apache
6. Run Container
cmddocker run -d -p 8088:80 docker-web2
7. Test in Browser

Go to http://localhost:8088
You see the form
Type your name → Click Submit
Next page shows: "Hello, Atharva!"


What to Show in Practical

Dockerfile content
index.html content
submit.php content
Browser showing the form
Browser showing dynamic response after submit
Docker Desktop showing container
