# Continuous-Deployment-with-Jenkins-and-Python
Let's set up a Continuous Deployment (CD) pipeline using Jenkins for a Python application. This project will involve setting up a Python application, creating a Jenkins pipeline to automate the build, test, and deployment processes.



Let's set up a Continuous Deployment (CD) pipeline using Jenkins for a Python application. This project will involve setting up a Python application, creating a Jenkins pipeline to automate the build, test, and deployment processes.

### Project Outline

1. **Setup GitHub Repository:**
   - Create a GitHub repository for a simple Python application.
   - Include a `requirements.txt` file and a simple test suite.

2. **Setup Jenkins:**
   - Configure Jenkins to pull the code from the GitHub repository, run tests, and deploy the application.

3. **Deployment Script:**
   - Write a deployment script to be executed by Jenkins.

### Detailed Steps

#### Step 1: Setup GitHub Repository

1. Create a new repository on GitHub (e.g., `python-cd-app`).
2. Initialize a simple Python application in the repository.

**Sample Python Application:**

- `app.py`:
    ```python
    from flask import Flask
    app = Flask(__name__)

    @app.route('/')
    def hello_world():
        return 'Hello, World!'

    if __name__ == "__main__":
        app.run(host='0.0.0.0', port=5000)
    ```

- `requirements.txt`:
    ```txt
    Flask==2.0.1
    ```

- `test_app.py`:
    ```python
    import unittest
    from app import app

    class FlaskTestCase(unittest.TestCase):
        def setUp(self):
            self.app = app.test_client()
            self.app.testing = True

        def test_home(self):
            result = self.app.get('/')
            self.assertEqual(result.status_code, 200)
            self.assertEqual(result.data.decode(), 'Hello, World!')

    if __name__ == '__main__':
        unittest.main()
    ```

#### Step 2: Setup Jenkins

1. **Install Jenkins:**
   - Install Jenkins on your server or use a cloud-based Jenkins service.

2. **Install Necessary Plugins:**
   - Install the following Jenkins plugins:
     - Git Plugin
     - Pipeline Plugin
     - SSH Agent Plugin (if deploying to a remote server)

3. **Create a Jenkins Pipeline:**
   - Configure a Jenkins pipeline job.

**Jenkinsfile:**

```groovy
pipeline {
    agent any

    environment {
        PYTHON_ENV = '/path/to/your/python/environment'  // Optional if using a virtual environment
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-username/python-cd-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'python -m unittest discover'
            }
        }

        stage('Deploy') {
            steps {
                sh './deploy.sh'
            }
        }
    }
}
```

#### Step 3: Deployment Script

Create a deployment script (`deploy.sh`) to deploy the application.

**deploy.sh:**

```sh
#!/bin/bash

# Define your deployment server details
DEPLOY_USER=your-username
DEPLOY_HOST=your-server-ip
DEPLOY_PATH=/path/to/deploy

# Archive the application code
tar czf app.tar.gz app.py requirements.txt

# Copy the archive to the deployment server
scp app.tar.gz ${DEPLOY_USER}@${DEPLOY_HOST}:${DEPLOY_PATH}

# SSH into the deployment server and deploy the application
ssh ${DEPLOY_USER}@${DEPLOY_HOST} << 'EOF'
cd /path/to/deploy
tar xzf app.tar.gz
pip install -r requirements.txt
nohup python app.py &
EOF
```

#### Step 4: Configure Jenkins Job

1. **Create a New Pipeline Job:**
   - Open Jenkins and create a new pipeline job.
   - In the job configuration, set the pipeline definition to "Pipeline script from SCM".
   - Configure the SCM to use your GitHub repository and point to the `Jenkinsfile`.

2. **Run the Pipeline:**
   - Trigger the Jenkins job manually or set up a webhook to trigger the job on every push to the repository.

### Conclusion

By following these steps, you will set up a continuous deployment pipeline for a Python application using Jenkins. This project demonstrates the essential steps for integrating version control, automated testing, and deployment in a continuous deployment pipeline, helping you to gain practical experience with these critical DevOps practices.
