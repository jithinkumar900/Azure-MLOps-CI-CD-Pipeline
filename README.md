# MLOps CI/CD Pipeline in Azure

## Overview
This repository contains the code and configurations for setting up a CI/CD pipeline for Machine Learning Operations (MLOps) using Azure DevOps and Azure v2 Accelerator. The pipeline automates the process of building, testing, and deploying machine learning models, ensuring consistency, reliability, and scalability in the ML development lifecycle.

Requirements


- An Azure subscription
- Azure DevOps account
- Azure v2 Accelerator

## Setup

1. **Azure Resources**: Set up the required Azure resources using Azure v2 Accelerator. This includes infrastructure as code for compute instances, storage, networking, etc., needed for ML model training and deployment.

2. **Azure DevOps Project**: Create a new project in Azure DevOps to host your CI/CD pipeline.

3. **Service Connections**: Set up service connections in Azure DevOps to access your Azure resources. These connections will enable the pipeline to interact with Azure services such as Azure Machine Learning, Azure Container Registry, etc.

4. **Pipeline YAML**: Define your CI/CD pipeline using YAML syntax. This pipeline should include stages for building, testing, and deploying your machine learning models. You can leverage Azure DevOps tasks and Azure CLI commands to orchestrate the pipeline workflow.

5. **Trigger**: Configure triggers for your pipeline to automatically run on code commits, pull requests, or other events in your repository. This ensures that your pipeline stays up-to-date with changes in your codebase.

6. **Variables**: Define variables in your pipeline YAML for sensitive information such as Azure credentials, API keys, etc. Use Azure DevOps variable groups or Azure Key Vault integration for secure management of these secrets.

7. **Testing**: Include automated tests in your pipeline to validate the performance and accuracy of your machine learning models. This may involve running unit tests, integration tests, or even deploying models to a staging environment for validation.

8. **Deployment**: Implement deployment stages in your pipeline to deploy tested models to production or staging environments. Use Azure Kubernetes Service (AKS), Azure Functions, or other Azure services for model deployment, depending on your architecture.

10. **Monitoring**: Set up monitoring and logging for your deployed models using Azure Monitor, Application Insights, or other monitoring solutions. Monitor model performance, resource utilization, and user interactions to ensure optimal operation.

11. **Documentation**: Document your CI/CD pipeline setup, including architecture diagrams, pipeline YAML configurations, deployment instructions, and troubleshooting tips. This documentation helps onboard new team members and troubleshoot issues effectively.

## 10km above diagram view 
diagram view of the entire infra which we will be deploying 

![Screenshot 2024-04-21 170615](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/d7ed3bd7-9b13-4178-8a5c-a0b29174da27)

## Screenshots and steps.

First we have to create a service principal which will have adequate role such that it will be able to make adequate resources in the cloud. I created an application standards are creating 2 application ie 'prod' and 'dev'

![Screenshot 2024-04-20 152427](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/60cf6d32-d9a2-4b74-9f17-a38eb0b9da2f)

Now we create a secret for both the prod service principle and the dev service principle here we should store all this data as it will be needed afterward even the data which was shown after creating service principle 

![Screenshot 2024-04-20 153321](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/a2962da0-fd04-4a74-9f7d-ffd4afea37ec)

The service principle we have created have no permission so we give permission for it in. we go to IAM identity and access management 

![Screenshot 2024-04-20 153950](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/1a446fa4-1071-4652-8ff3-5728f80af8f6)

Then add role assignment 

![Screenshot 2024-04-20 154019](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/b95712cd-416a-4d4b-902c-e33705ff28be)

we go to the privelaged role and select contributor for both the dev and prod 

![Screenshot 2024-04-20 154137](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/f8aa5242-892f-4e2a-874a-3817ce75dc03)

we see that we have to choose both the service principle 

![Screenshot 2024-04-20 154245](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/5363f4a4-19e1-4f65-84b8-5ddbc4f11c0d)

we review and assing the role 

![Screenshot 2024-04-20 154442](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/32bc26ba-bd25-47c6-8bf6-15885c0aff76)

Now to use the azure v2 accelerator we will have to fork few directories ie 

https://github.com/Azure/mlops-templates/generate (with this we will create a repo in our github with the mlops-template)
Also https://github.com/Azure/mlops-v2  (for sparse_checkout.sh)
And https://github.com/Azure/mlops-project-template/generate (for content like iaac etc)

![Screenshot 2024-04-20 154744](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/f7ae1695-7218-4b81-acd4-29b1521b424c)

Now create an empty repository as this accelerator will create code from template from the based input and it need a place to host the mlops code bootstraped we will have mlops solution in it.

![Screenshot 2024-04-20 160059](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/451c8708-d912-410b-b5d0-4ae3163ca0b6)

now create a directory in your local machine and clone the directories to it ie the above 3 using git clone git clone https://github.com/jithinkumar900/name.git 

![Screenshot 2024-04-20 160356](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/1f61df5f-4855-419d-bad8-8e9bfbb801e0)

we see that in the v2 repo we have a shell ie sparse_checkout open it and give the needed changes to it 

![Screenshot 2024-04-20 161148](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/7180649b-34c9-4aa3-ad21-2df7e86d7368)

![Screenshot 2024-04-20 164344](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/b267b9b9-78de-458f-a259-2e150062b92e)

Now we will have to do authentication to connect to github from the local machine 

We will create the local key 

> ssh-keygen -t ed25519 -C “jithinkumar900@gmail.com” (it will create a local key on local machine)

Here -t ed25519 is an algorithm to generate the key 

And -C is the comment 

> eval "$(ssh-agent -s)"

Used to start the ssh agent 

> ssh-add ~/.ssh/id_ed25519

Adding the private key to the ssh agent 

> cat ~/.ssh/id_ed25519.pub

Add the ssh key to the github . here copy the ssh key 

![Screenshot 2024-04-20 170003](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/faa684ad-7347-47de-b17f-44e72bc4eb97)

Now go to github setting ssh and gpg key create new key and add the secret key in the github 

![Screenshot 2024-04-20 170243](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/7b81dc3e-6149-4582-81ae-0714b7d9fb88)

after we have added the key to the github it will be able to perform action based on command from our local machine here we will run the shell which we modified earlier 

![Screenshot 2024-04-20 170640](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/8e586073-7d64-4eb8-97a0-2b8e68e59128)
![Screenshot 2024-04-20 170708](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/030b5860-23be-4f6a-a329-98d1032bd67c)

we see that the empty repository which we have is been filled with the requeired iaac etc 

![Screenshot 2024-04-20 183104](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/bacf4d4f-d245-47fb-902e-4dde90a6e0a9)

now we navigate to the azure devops create a workspace there and then in that we will run our pipeline 

![Screenshot 2024-04-20 183324](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/6994ab42-840a-4306-b4ad-7582dc5dd168)

Now in azure devops we will import the repository 

![Screenshot 2024-04-20 183627](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/ace8ceb7-106e-466e-beeb-489dc3cc7640)

Now we have to create the pipeline but we have to provide permission ,how using the service principle we have created 

For that go to the project settings  then > service connection 

Create service connection for dev and prod 

Subscription id - 916bda52-355b-461a-b8bd-74f10eab0c83

Name -  Free Trial

In azure devops create the service connection there create new connection 

In there azure resource manager > service principal manul, here paste the required id’s

![Screenshot 2024-04-20 183943](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/5572ffea-9fa6-42cd-9545-6af76183aa7a)

![Screenshot 2024-04-20 184126](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/7a721ffd-b5cc-46e9-9322-03e06ff57f47)

![Screenshot 2024-04-20 184152](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/d3168aa3-d7b3-4464-b6f1-9a8aa914ea7d)

![Screenshot 2024-04-20 231353](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/d5e7412f-879d-4883-9f3f-30469b04dd15)

we see the 2 service connection 

![Screenshot 2024-04-20 233025](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/2f337e85-5694-4bd9-b828-b732728a79cf)

Now we have to connect azure devops with the github 

Go to setting > developer setting > token classic > generate token 
Give permission 

Repo, admin-repo-hook and user 

Token 
ghp_CRW8ylXA5c8m8k9zc8weFdsS1

We have made the github connection 

go to developer setting 

![Screenshot 2024-04-20 233151](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/f031f10b-6b18-4202-9df4-c82ae8e42ab7)

click on personal access token 

![Screenshot 2024-04-20 233202](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/022b97e6-88a6-4bd0-a407-ee2c7b3f10ea)

generate the token ![Screenshot 2024-04-20 233230](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/9e0a1a90-b4eb-4774-aded-9932062214ce)

give reqired permission - Repo, admin-repo-hook and user 

![Screenshot 2024-04-20 233503](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/88cce130-2be3-4887-bbcd-ea21eb0800d3)

paste this key in service conncetion and link github and azure devops 

![Screenshot 2024-04-20 233752](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/4f647cec-4c63-4ceb-b388-9045538afee5)

configure the following infra yaml and set the postfix number to a unique one in both dev and prod 
![Screenshot 2024-04-20 234239](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/3b2281cf-07c9-4d1d-8fda-4e0f3469f68a)

![Screenshot 2024-04-20 234309](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/0c0fbcd2-0108-459f-893c-8245a5c200ca)

now run the infracture pipeline, click on pipeline

![Screenshot 2024-04-20 234421](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/f131eff4-4ea6-4738-b904-1b656a3b9112)

click on exesting azure pipeline 

![Screenshot 2024-04-20 234435](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/c5cf6713-0e45-4e80-85d1-83aa1b194660)

review it and run it 

![Screenshot 2024-04-20 234509](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/239a2c0a-de85-4875-b37b-b9c7b4b18dd9)

we see that the pipeline has been deployed 

![Screenshot 2024-04-21 091400](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/992cbb86-f0ba-425d-99be-cf5493a0924d)

we see that the various resources have been created 

![Screenshot 2024-04-21 091427](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/6844f1c0-9cf8-495d-a571-a4a83f6e6d42)

now we can do the same pipeline run from git hub for that we set the role using cli, we have already done that but we need the content which will be shown after the command is executed so we run the command and then copy the id's

az ad sp create-for-rbac --name “Azure-ARM-Dev-projectname(your service principal name)” --role contributor --scopes /subscription/sldjfwouos0df9808234-yourid --sdk-auth

![Screenshot 2024-04-21 092246](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/e8ea53d2-1b98-439e-8cbd-f036d1bb3057)

![Screenshot 2024-04-21 092334](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/114cf2ee-e745-4e31-8f74-d78493494d4d)

In setting secret > actions > new repo use ‘AZURE_CREDIANTIALS’ and paste the above 

![Screenshot 2024-04-21 190321](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/5ad70a4b-1f76-4765-8129-bb4276483b68)

and make sure the pipeline for training use the same name as the cred 

![Screenshot 2024-04-21 092506](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/2cdbe872-db08-42a6-83d3-2f246db4ff3a)

now we run the training pipeline 

![Screenshot 2024-04-21 190458](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/70b5720b-af6a-40c0-8d31-48e54ab921c5)

now when we come back to the azure devops we can see the pipeline jobs created 

![Screenshot 2024-04-21 093139](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/888dbd30-1937-492e-9252-9d894c9bb8ff)

we used default model taxi-model this can be changed to need 

![Screenshot 2024-04-21 102831](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/5d663d00-b71e-4a84-aa11-0fc486f21f32)

resposible ai data is also show after running the training pipeline 

![Screenshot 2024-04-21 102908](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/1ab074a8-b044-4289-a9ee-28b8e371dec6)

now to create a endpoint we run the endpoint pipeline and run it this can be done in both the azure devops / github here we have done it using github

![Screenshot 2024-04-21 103048](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/1af9d17a-a0c0-4ae6-b91e-cfdaa2f2491c)

the pipeline has been created 

![Screenshot 2024-04-21 103116](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/8678e235-bb11-4a2b-9b87-4f1bb3553eec)

we can go to the endpoint section in azure devops and then we can access the endpoint 

![Screenshot 2024-04-21 103319](https://github.com/jithinkumar900/azure-mlop-ci-cd-pipeline/assets/59408287/0a9705b3-0a75-48ba-b3da-9eb88211c78d)

with this out end to end pipeline is done where first the infra is made and then we train it and then we deploy it and get the endpoint which user can interact 

## conclusion 

This comprehensive end-to-end pipeline seamlessly integrates infrastructure provisioning, training, and deployment, culminating in the creation of a user-accessible endpoint. Beginning with the establishment of robust infrastructure, we ensure a solid foundation for subsequent stages. Following this, our streamlined training process optimizes model performance, paving the way for deployment. Finally, the deployment phase brings the model to life, providing users with a seamless interface for interaction. Together, these steps encapsulate a cohesive workflow, empowering users to effortlessly engage with the deployed solution.
















