# gitlab-pipeline-deploy-gcp

## This yml file is opinionated since i use it for my personal proejct. 

```yml
variables:
  GCP_ZONE: us-central1-a

stages:
  - build
  - deploy

build:
  image: node:12.14.0
  stage: build
  only:
    - master
  script:
    - npm install

deploy:
  stage: deploy
  image: google/cloud-sdk:latest
  only:
    - master
  dependencies:
    - build
  script:
    #
    # Authenticate using the service account stored here: https://gitlab.com/groups/{YOUR_GITLAB_PROJECT}/-/settings/ci_cd
    - echo $GCLOUD_SERVICE_KEY > ${HOME}/gcloud-service-key.json
    - gcloud auth activate-service-account --key-file ${HOME}/gcloud-service-key.json
    # Register GCP Project
    # - gcloud auth activate-service-account --key-file=$GCLOUD_SERVICE_KEY
    - gcloud config set project $PROJECT_ID
  
    # Deploy
    - gcloud beta functions deploy $FUNCTION_NAME --runtime nodejs10 --trigger-http

```
