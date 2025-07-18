# Cloud-based MLFlow for tracking experiments & deploying ML-powered App to AWS

Data used:
https://archive.ics.uci.edu/dataset/186/wine+quality

## Part-1: Running ML-powered App locally

0. Create a GitHub repo & push the files from this project there.
Project's name should be mlflow_aws

1. Create an account with https://dagshub.com using your gitHub account

2. Import a project from Github repo & copy credentials from Repo -> Remote -> Experiments 
& paste them below under the `MLflow (dagshub)` section

3. Export variables by running commands from `MLflow (dagshub)` section below in your terminal

4. Create a new virtualenv to test the app locally & install dependencies
```python3 -m venv .mlflow_awsenv```
```source .mlflow_awsenv/bin/activate```
```pip install -r requirements.txt```

5. Run the pipeline
```python3 main.py```

6. Check out logs populating locally in logs/running_logs.log

7. Check out model's metrics in artifacts/model_evaluation/metrics.json
Check out experiments' metrics in Cloud-based MLFlow

8. Change parameters values in params.yaml & relaunch the experiment

9. Repeat steps #6 & #7

10. Run the app that uses the model, exposes the predicting endpoint & serves the UI
```python3 app.py```

11. Train the model locally
Navigate to http://127.0.0.1:8080/train

12. Interact with the UI
Navigate to http://127.0.0.1:8080/predict


### Change the parameters in the folowing config files to adapt the project:

1. config.yaml -> for the overall sequence of steps & atrifats file names
2. schema.yaml -> for data validation specifics
3. params.yaml -> for ML model trained specification
5. components/... -> for modifications in each of the pipeline steps
7. pipeline -> for stages names
8. main.py -> for steps sequence & basic logging logic
9. app.py -> for Flask microservice endpoints names, port to use & locations of Front-end assets
10. .github/workflows/mail.yaml line #95 "--name=YOUR_AWS_PROJECT_NAME"


## Part-2: MLflow (dagshub)

1. Set up an account with dagshub.com
2. Import your github repo
3. Modify src/mlflow_aws/config/configuration.py-> get_model_evaluation_config() in line #106 to your dagshub project URL
4. Get the parameters from dagshub UI:
MLFLOW_TRACKING_URI=https://dagshub.com/migumax/mlflow_aws.mlflow \
MLFLOW_TRACKING_USERNAME=migumax \
MLFLOW_TRACKING_PASSWORD=9cbae1b719fbb930f24346db8542134dec87fff6 \
python script.py

5. Run this to export as env variables in you local terminal (after adapting the values to yours):

```shell

export MLFLOW_TRACKING_URI=https://dagshub.com/migumax/mlflow_aws.mlflow

export MLFLOW_TRACKING_USERNAME=migumax 

export MLFLOW_TRACKING_PASSWORD=9cbae1b719fbb930f24346db8542134dec87fff6

```
6. Run main.py, modify params.yaml & run main.py again

7. Explore the changes in MLFlow UI in dagshub


## Part-3: Deploying ML-powered App to AWS Cloud using EC2 & ECR

1. Login to your AWS console

2. Create a new IAM user that we'll use for deployment: mlflow_aws

	Add 2 policies for the new user:
	* AmazonEC2ContainerRegistryFullAccess 
	* AmazonEC2FullAccess

Create Access keys pair
	
3. Create ECR repository to host our Docker image (use the same name)
    
	- Input your ECR URI here for future use: 042386354197.dkr.ecr.eu-central-1.amazonaws.com/mlflow_aws

4. Create a new EC2 machine (Ubuntu 22.04, t2.large, 32GB SSD) 
(use the same name as for everything else)

5. Configure the machine by installing all the packages needed:

	```sudo apt-get update -y```

	```sudo apt-get upgrade```

	```curl -fsSL https://get.docker.com -o get-docker.sh```

	```sudo sh get-docker.sh```

	```sudo usermod -aG docker ubuntu```

	```newgrp docker```

	```docker --version```
	
6. Configure EC2 machine to be a self-hosted runner. For that Open your project in Github and do:
    settings -> actions -> runner -> new self hosted runner -> choose os -> run the sequence of commands suggested

	These commands need to be ran inside your EC2 virtual machine's shell

7. Setup github secrets inside your Github project:

    * AWS_ACCESS_KEY_ID

    * AWS_SECRET_ACCESS_KEY

    * AWS_REGION

    * AWS_ECR_LOGIN_URI

    * ECR_REPOSITORY_NAME

8. Make sure you uncomment contents of the file & upgate your AWS project name here: .github/workflows/mail.yaml line #94 "--name=YOUR_AWS_PROJECT_NAME"

9. Trigger the CICD pipeline by doing the standard git workflow steps
```git add .```
```git commit -m"added cicd to the project"```
```git push```

10. Explore the pipeline steps progression in Github actions UI

11. Navigate to the deployed App by inputting <your_ec2_machine_address>:8080 in your browser

Train the model first by goint to <your_ec2_machine_address>:8080/train

Interact with the App to get predictions based on user's inputs

12. Clean the resources in AWS not to get charged extra money! 


