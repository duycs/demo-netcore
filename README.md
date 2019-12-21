# demo-net-core
This article with some tutorials will help you study and deploy a demo project:
- Tutorial_20/12/2019: Create a ASP.NET Core project then deploy to Heroku server via CircleCI pipeline.
- Tutorial_x/x/x: Design a database that description your ideas.
- Tutorial_x/x/x: Design some parttern and layer common for your project.
- Tutorial_x/x/x: Create a middle ware for authorization and authentication.
- Tutorial_x/x/x: Create a RESTful API for your ideas.
- Tutorial_x/x/x: Create a gRPC API for your ideas.
- Tutorial_x/x/x: Create your Angular landing page then hosting to AWS-S3 service.
- Tutorial_x/x/x: Use an ORM and implement CQRS.
- Tutorial_x/x/x: ...

### Keywords: 
- ASP.NET Core
- Docker
- Heroku
- CircleCI

---
# Tutorial_20/12/2019: Create a ASP.NET Core project then deploy to Heroku server via CircleCI pipeline

### You learn a little bit about concepts:
#### Keywords
- ASP.NET Core
- Docker
- Heroku
- CircleCI

### You will use Heroku to host and run your ASP.NET Core application.
You will have Docker pack the web application inside of the container, and then use CircleCI to deploy the Docker container to Heroku Container Registry.


## Setup your local development with:

### 1. ASP.NET Core running on .NET Core 
This demo is version 2.2, but you can use latest version.
Download installers of SDK and Runtimes compatible with your operating system:
https://dotnet.microsoft.com/download/dotnet-core/2.2

### 2. Docker
Download docker desktop compatible with your operating system:
https://docs.docker.com/docker-for-windows/install/
https://docs.docker.com/docker-for-mac/install/

### 3. Your project idea
Do you have any idea?
Make it to leaning and creativily!

## Steps

### 1. Create your project or use this artilce's project
#### 1.1 Create new your project
Create a new folder for your project.
Open command/Terminal then run this command to create a template webapi project:
```
  dotnet new webapi
```
#### 1.2 Dockerize ASP.NET Core application
To dockerize your application, you need to create new Dockerfile in your project folder.
Here is a complete Dockerfile looks like:

```docker
# Grab an image with a small OS image made for .Net Core
FROM microsoft/dotnet:2.2-sdk AS build-env
# Working directory is /app
WORKDIR /app

# Copy csproj and restore as distinct layers
COPY *.csproj ./
RUN dotnet restore

# Copy everything else and build
COPY . ./
RUN dotnet publish -c Release -o out

# Build runtime image
FROM microsoft/dotnet:2.2-aspnetcore-runtime
WORKDIR /app
COPY --from=build-env /app/out .

# ENTRYPOINT ["dotnet", "demo-net-core.dll"]
# heroku uses the following
CMD ASPNETCORE_URLS=http://*:$PORT dotnet demo-net-core.dll
```

#### 1.3 Config CI/CD pipeline
Create a folder .circleci in the root folder of your repository.
Create a new file named config.yml inside that folder.
Here is a complete config.yml looks like:

```
version: 2
jobs:
 build:
   machine: true
   steps:
     - checkout 

     # build image
     - run: |         
         docker info
         docker build -t aspnetapp -f Dockerfile .
     # deploy the image
     - run: |         
         docker login --username=$HEROKU_USERNAME --password=$HEROKU_API_KEY registry.heroku.com
         docker tag aspnetapp registry.heroku.com/$HEROKU_APP_NAME/web
         docker push registry.heroku.com/$HEROKU_APP_NAME/web                
         curl https://cli-assets.heroku.com/install.sh | sh
         heroku container:release web -a $HEROKU_APP_NAME
```

### 2. You can Fork the complete code of this article on GitHub to become your project will be deployed:
https://github.com/duycs/demo-net-core

### 3. Create your Heroku application
Log in to Heroku or Sign up to create a new account if you don’t have one:
https://id.heroku.com/login
After that, create a new app, choose the name for your application and select the desired region.
Save the name for later, we will use it with CircleCI.

### 4. Set up your project on CircleCI with Heroku
Login to CircleCI with your Github:
https://circleci.com/vcs-authorize/
Then grant authorization your Github Repository for CircleCI
On CircleCI dashboard, click ADD-PROJECTS, chooe your project which you want to deploy on Heroku, Click Set-Up-Project.
At Set up project, choose Operating System is Linux then click Start-building
After that is done, you will need to set up environment variables for your CircleCI project at Setting:
```
HEROKU_API_KEY: You can find your Heroku API key in your Heroku Account settings
HEROKU_USERNAME: Your username should be your email that you use to sign in
HEROKU_APP_NAME: The name that you used when you were creating new Heroku application.
```

### 5. Coding and push to master branch, after a few minutes, go to your Heroku application domain and enjoy your change!

# References:
- ASP.NET Core:
https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio

- Docker:
https://docs.docker.com/get-started/

- Docker containter a dotnetcore app:
https://developer.okta.com/blog/2019/09/18/build-a-simple-dotnet-core-app-in-docker
https://softchris.github.io/pages/dotnet-dockerize.html#build-our-image-start-container

- CircleCI
https://circleci.com/docs/2.0/configuration-reference/

- Deploy a dotnetcore app to Heroku via circleci:
https://codingblast.com/hosting-asp-net-core-on-heroku-with-dockercircleci-for-free/

---
