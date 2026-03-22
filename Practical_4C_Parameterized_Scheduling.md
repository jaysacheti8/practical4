# PRACTICAL 4C — Parameterized Scheduling

**Student:** Atharva Ahirrao | **GitHub:** https://github.com/Atharvaahirrao/pract5

---

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
