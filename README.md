## NodeJS Deployment To OpenShift Demo

We have plenty of options to deploy NodeJS Application to OpenShift, in this demo we will see some of these deployment options, this repo is fork from the following Git repo: https://github.com/sclorg/nodejs-ex


### Using S2I from the Console

Go to OpenShift Developer Console, Select NodeJS from the catalog and click on Create: 

<img width="418" alt="Screenshot 2024-07-08 at 4 04 08 PM" src="https://github.com/osa-ora/nodejs-demo/assets/18471537/a9a9b74a-4183-4bcb-947f-6d1804f2c224">

Fill in the Git Repo location "https://github.com/osa-ora/nodejs-demo" and application name:

<img width="703" alt="Screenshot 2024-07-08 at 4 05 31 PM" src="https://github.com/osa-ora/nodejs-demo/assets/18471537/d2a10966-06ba-4d5f-a6eb-0d217d4f8a04">

Select Build options as "Builds" and click on create.

<img width="687" alt="Screenshot 2024-07-08 at 4 06 26 PM" src="https://github.com/osa-ora/nodejs-demo/assets/18471537/a99cb96e-ddcf-4b5c-b7a8-506371df3a87">

Note: If you are using private NPM artifact repository, then you can just add an environment variable; NPM_MIRROR to point to this private artifact repository.
<img width="1065" alt="Screenshot 2024-07-08 at 4 09 46 PM" src="https://github.com/osa-ora/nodejs-demo/assets/18471537/db13e80b-0e46-4e9e-8223-d196f066995d">

The application will built and deployed into OpenShift and you can just test it by using the route:
<img width="972" alt="Screenshot 2024-07-08 at 4 12 32 PM" src="https://github.com/osa-ora/nodejs-demo/assets/18471537/1e96d265-69b8-4283-bfd9-a554428f2ddf">

In order to use the CRUD operations in the application, you need to deploy a Postgres DB, but this is not relevant to our NodeJS app deployment so we will not do it.


### Using Tekton Pipeline from the Console

Follow the same process but during the selection of Build Options select "Pipeline" option as following:

<img width="700" alt="Screenshot 2024-07-08 at 4 17 36 PM" src="https://github.com/osa-ora/nodejs-demo/assets/18471537/94a0406e-b0f3-4dc2-861d-1004e12497f2">

Follow the progress on the pipeline execution and once successfully finished, check the application deployment status.

### Using Binary Build

Execute the following commands from your local machine 

Optionally you can install the dependencies, and in that case it will be a pure binary build, otherwise the builder image will install the dependencies for you.
```
npm install
```

Deploy the application as a binary build.
```
oc new-project dev
oc new-app --image-stream=openshift/nodejs:16-ubi8 --name=my-nodejs-app .
oc start-build my-nodejs-app --from-dir=.
oc expose service/my-nodejs-app
```

You can also add the NPM_MIRROR environment variable to the build in case of locally configured repsository dependencies.

### Using Builds for OpenShift: (TBC)

First you need to install the Builds for OpenShift Operator:

<img width="276" alt="Screenshot 2024-07-08 at 11 54 49 AM" src="https://github.com/osa-ora/angular-demo/assets/18471537/f2f5d78d-e95a-43f6-a9bf-a5fb4a70f6df">

Once, installed create an instance of "Shipwright Build", keep the default

<img width="696" alt="Screenshot 2024-07-08 at 12 32 03 PM" src="https://github.com/osa-ora/angular-demo/assets/18471537/ea1641f3-df9d-4dfd-86bd-9c27ade1e177">

Now, you can deploy the application by creating a Shipwright build either from the console or from the command line:

```
//create an openshift project
oc new-project dev

//create shipwright build for our application in the 'dev' project

```









Original Readme document: 

![Node.js CI](https://github.com/nodeshift-starters/nodejs-rest-http-crud/workflows/ci/badge.svg)
[![Coverage Status](https://coveralls.io/repos/github/nodeshift-starters/nodejs-rest-http-crud/badge.svg?branch=master)](https://coveralls.io/github/nodeshift-starters/nodejs-rest-http-crud?branch=master) 

Example CRUD Application

### Getting Started

#### Running Locally

First, install the dependencies

`npm install`

A Postgres DB is needed, so if you are using Docker, then you can start a postgres db easily.

`docker run --name os-postgres-db -e POSTGRESQL_USER=luke -e POSTGRESQL_PASSWORD=secret -e POSTGRESQL_DATABASE=my_data -d -p 5432:5432 centos/postgresql-10-centos7`

In this example, the db user is `luke`, the password is `secret` and the database is `my_data`

You can then start the application like this:

`DB_USERNAME=luke DB_PASSWORD=secret ./bin/www`


Then go to http://localhost:8080


Other options:

* `npm run dev` same as `npm start` but with pretty output log.
* `npm run dev:debug` shows debug information.


#### Running on Openshift

First, make sure you have an instance of Openshift setup and are logged in using `oc login`.

Then create a new project using the `oc` commands

`oc new-project fun-node-fun`

For this example, you will also need a postgres db running on your Openshift cluster.

`oc new-app -e POSTGRESQL_USER=luke -ePOSTGRESQL_PASSWORD=secret -ePOSTGRESQL_DATABASE=my_data centos/postgresql-10-centos7 --name=my-database`

Then run `npm run openshift` to deploy your app

Run the following command to show the newly exposed route that you can navigate:
```
oc get route nodejs-rest-http-crud
NAME                    HOST/PORT                                        PATH   SERVICES                PORT   TERMINATION   WILDCARD
nodejs-rest-http-crud   nodejs-rest-http-crud-opentel.apps-crc.testing          nodejs-rest-http-crud   8080                 None
```
#### Running on Openshift with traces enabled

* [Read more](./OTEL.md)
