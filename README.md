# Collection<GoodToKnowCommands>

This is a small collection of useful and good to know commands for the most used tools in the Integration Team at Tink.
From the perspective of someone that has started very recently and keeps asking the same stuff over and over!

--------------------------------------------------------------

## Kubernetes

--------------------------------------------------------------

### Kubectl

To see different components, we use the command "`kubectl get`" follow by the component we want to check:

*Pods*
`kubectl get pods`

*Deployments*
`kubectl get deployments`

*Services*
`kubectl get svc`

To shutdown the components or creating them, we use "`kubectl delete`" or "`kubectl create`" respectively,
based on our purpose:

*Delete services, we can write the component name*
`kubectl delete svc <serviceName>`

*Delete deployments, we can also delete all the components of that type with the flag "`--all`"*
`kubectl delete deployments --all`

*Create the development databases from Tink, the "`-f`" flag allows us to specify a file to create it from*
`kubectl create -f tink-infrastructure/kubernetes/development/`

*Create a specific service intead of the whole Tink database structure, you can select the specific **yml** file for it*
`kubectl create -f tink-infrastructure/kubernetes/development/maindb.yml`


*Useful for debugging is to get the logs from **pods***
`kubectl logs <podName>`

If we want to be able to interact with a **pod**, we start and interactive session with it. First we specify the flag "`-it`", to pass standard input as TTY to the container, "`kubectl exec -it <podName> <InteractiveType>`". We then specify the **pod** and the interactive session type we want to use:

*Run an interactive session with "`/bash/bin`", "`mysql`" or some other command in the specified **pod** which you can get with "`get pods`" command*
`kubectl exec -it bigdb-694794dc78-h268h /bin/bash`

### Minikube

If you want to delete everything (whole instance) of your Minikube it can be done easily. It is good to try this if your "`kubectl`" commands stop working for any reason, afterwards you can just set it up again from the Tink infrastructure folder with one script: 

*Delete a Minikube instance*
`minikube delete`

*Setup Minikube, run the following script from **/tink-infrastructure***
`./setup-minikube.sh`

A useful thing of using Minikube is that we can enable addons and we can use a dashboard to monitor and interact with Kubernetes. For container cluster monitoring and performance analysis we can enable **Heapster** and we can also launch a **Dashboard**:

*Enable heapster addon*
`minikube addons enable heapster`

*Start a dashboard on your web browser*
`minikube dashboard`

--------------------------------------------------------------

## Bazel

--------------------------------------------------------------

Bazel is used to build and run the Tink backend locally on our laptops (we use Forego together with it to make it simpler). Most of it is done with scripts and configuration files but we still can run some additional commands and tweak files to make adjustment if things are not working properly. The order for running the backend depends on what you are doing:

0. (Optional) Cleaning database, if you have restarted the backend and want a fresh state.
`bazel run :system seed-database etc/development-minikube-system-server.yml`


1. (*Optional-ish*) Seeding database is a must before running start for the first time, everytime the database is reset (Kubernetes or Cleaning command) or when a change in the *"providers-XX.json"* file is done. Otherwise if only agents or controllers are changed, you can skip it.
`bazel run :system clean-database etc/development-minikube-system-server.yml` 


2. To start all the different services needed for the complete backend flow to run locally, we give the command a file to use for this with the "`-f`" flag `forego start -f Procfile.minikube`


The Bazel commands will do a build before executing the seeding or cleaning, which can be slow the first time it is done. You can always do a build manually everytime you make changes to the code to check if it all compiles.

*Building everything with bazel*
`bazel build :all`

The Procfile contains all the configurations for each service. All the services in the file will be started with Forego, if you want to skip some of them for any reason you can comment it out before starting it up.

--------------------------------------------------------------

## Accessing Databases

--------------------------------------------------------------

When we do testing locally, there are a few ways to access the different databses we use depending on what we are working with. The Main database uses MySQL while the Big database uses Cassandra. In Casandra you can find transactions, investments, transfers, etc. While in MySQL you can find accounts, categories, users, etc.

The way to access the databases is quite similar. We just need to start an [interactive session](#Kubectl) using either "`mysql`" or "`cqlsh`" with the **pod** for the database we want to use. 

*Cassandra would be the following command for example*
`kubectl exec -it bigdb-694794dc78-h268h cqlsh`

When the session is started, we do the following steps inside either the mysql or the cqlsh shell:

*Make sure we are reading the Tink tables*
`use tink;`

Next we can use one of the following commands to see what is in the database:

*For MySQL*
`show tables;`

*For Cassandra*
`describe tables;`

From here on we need to use MySQL or Cassandra commands to navigate the database and work with it.

--------------------------------------------------------------

## Git

--------------------------------------------------------------

There are definetly a lot of commands when it comes to Git but this short list is based on the ones that could be considered the most useful when recently onboarded. 

*It is always great to check the status of the branch you are working on, specially after making any changes or before commiting*
`git status`

The checkout command can be used for many purposes, a couple of them are:

*To checkout the specified branch*
`git checkout <branchName>`

*To discard unstaged changes in a branch, either a specific file/directory or everything*
`git checkout -- <fileName or directory>`
`git checkout -- .`


git stash

git branch

git rebase

git reflog

*To make a commit, using the "`-m`" flag allows the message to be added in the command right away inside the quotation marks*
git commit -m "fea(Agent) changes added"`


*To add which unstaged file or directory should be added to the commit*
`git add <fileName or Directory>`


--------------------------------------------------------------

## Python

--------------------------------------------------------------
