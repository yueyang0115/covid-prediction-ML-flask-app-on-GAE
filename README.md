# Covid Prediction Flask App on GAE
Duke IDS 721 Final Project  
Author: Yue Yang, Jingyi Xie, Jiateng Mao

## Overview
In this project, we built a **machine learning** model to predict the number of Covid positive cases and deployed it on **Flask**. The **Flask** app is deployed to **Google App Engine(GAE)** and can be accessed through a public url. We configured **Cloud Build** for **continuous deployment(CD)**. We also verified the elastic scale-up performance via load test with **Locust**.  
* Application Deployed on: https://covid-prediction-311000.uc.r.appspot.com
* ML Framework: Sklearn
* Platform: Flask + Google App Engine
* Load Test Framework: Locust
* Dataset: https://raw.githubusercontent.com/jingyi-xie/covid-prediction/main/national-history-update.csv  

The workflow of this project is as below:  
<img width="1279" alt="architecture2" src="https://user-images.githubusercontent.com/44473421/116327493-e2fa8780-a794-11eb-8b69-744e226124a5.png">

Here is an image of the frontend website:  
![CleanShot 2021-04-20 at 22 06 08](https://user-images.githubusercontent.com/49466651/115486455-ad015480-a224-11eb-850e-4a5913e8c605.png)
Here is an image of the load test result. It shows that our app can scale to 1K+ requests served by multiple endpoints.    
![CleanShot 2021-04-20 at 21 42 03](https://user-images.githubusercontent.com/49466651/115485025-e84e5400-a221-11eb-8285-4a62499597d4.png)

## Reference
Souece Code: [github.com/noahgift/container-revolution-devops-microservices](https://github.com/noahgift/container-revolution-devops-microservices)  
Souece Code: [github.com/johnnyAklilu/USCOVID19CasePredictorUpdated](https://github.com/johnnyAklilu/USCOVID19CasePredictorUpdated)  
Souece Code: [github.com/chugh007/FlightPricePrediction](https://github.com/chugh007/FlightPricePrediction)  
Souece Code: [github.com/yueyang0115/flask-app-GCP-deploy](https://github.com/yueyang0115/flask-app-GCP-deploy)  

## How to build from scratch
Read through the files and understand how it works.  
* ```main.py``` is the main workflow of the project. It trains model and directs to the flask web. 
* ```national-history-update.csv``` is the dataset for training the ML model.
* ```Makefile``` is used for installing packages and others.
* ```requirements.txt``` is required for the Makefile.
* ```app.yaml``` is required for deployment on Google App Engine.  
* ```cloudbuild.yaml``` is required for continuous deployment with Cloud Build.
* ```locustfile.py``` is required for load test via locust.  
* ```predict-local.sh``` is required for testing the app which runs locally by sending POST request to it through script.
* ```predict.sh``` is required for testing the app which runs on cloud by sending POST request to it through script.
* ```/templates``` folder contains the templete html page for the flask app.  

To build this project from scratch, you can follow these steps:

### Set up project
Launch Google Cloud Platform, create a new project. Change your current project to it and activate Cloud Shell.  
To configure CD with Cloud Build, you will need to create your own github repo. Otherwise you can just git clone this repo and skip the steps below.  
Create ssh-keys and upload it to Github.
```
ssh-keygen -t rsa
```
Create a new repo on github, git clone it to your GCP local and cd into it. Upload all the files in this repo to your new repo.  


### Run this app locally
Create a virtual environment and activate it. (To deactivate it, run ```deactivate```).  
```
make set-up
source ~/.covid_venv/bin/activate
```
Install the required packages.  
```
make install
```
Run this app, the flask app will be running on http://127.0.0.1:8080/.  
```
python3 main.py
```
You can test it from the frontend website or send a POST request to the running app through a script.
```
bash predict-local.sh
```

### Deploy this app to GAE
(optional) Verfiy the current project is working. Switch your project if it's not what you want.  
```
gcloud projects describe $GOOGLE_CLOUD_PROJECT
gcloud config set project $GOOGLE_CLOUD_PROJECT
```
Create app engine in GCP.
```
gcloud app create 
```
When it asks you to choose a region, select one(in my case is **14 us-central**). Type "yes" when it asks you to continue.  
Deploy this app on cloud, the app will be running on the provided public url.  
```
gcloud app deploy
```
You can test it from the frontend website or send a POST request to the running app through a script. Remember to change the website address in ```predict.sh```.  
```
bash predict.sh
```

### Set up CD with Cloud Build
Documents:  [Automate App Engine deployments with Cloud Build](https://cloud.google.com/source-repositories/docs/quickstart-triggering-builds-with-source-repositories).  
Create **cloudbuild.yaml** file. Open GCP Cloud Build console.  
Select Trigger and create a new **trigger**, set repository event to "Push to a branch", connnect your related github repository as the source to watch for events, chosse master branch.  
Select "settings", then click on "Cloud Build service account". In the newly open documenation, click on "Open the Cloud Build Settings page". In the new page, **enable App Engine and Service accounts**.  
<img width="1279" alt="gcp" src="https://user-images.githubusercontent.com/44473421/116343446-6d051900-a7b2-11eb-9318-4fedf8b8833d.png">

Now, if you make a new push, this app will be redeployed automatically and you can find the pipeline under Cloud Build.

### Load test with locust
Run following command, the locust server will be running on http://0.0.0.0:8089/.  
```
locust
```
Go to the webpage, fill out the form and try to test it.  
<img width="1279" alt="locust" src="https://user-images.githubusercontent.com/44473421/116341182-755b5500-a7ae-11eb-971f-ddef7019c78b.png">

### Done!
