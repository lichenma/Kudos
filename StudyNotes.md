# CRUD Flask React Application 

Acknowlegements - this application is inspired by the design provided by Kleber Correia at okta 

Flask will be used in this project to quickly put together a REST API, meanwhile React is a declarative, efficicent and flexible JavaScript library for building the front-End. It faciliates the creation of a complex, interactive and stateful User Interface from small and isolated pieces of code called components. 


The first part of building this application will be creating the back-End REST API using Python: 

Ensure that Python 3 is installed using
```
python --version
``` 

The REST API will use some third-party libraries to help connect to a database, create schemas for the models, and validate the incoming requests. 


To assist with this, Python has a powerful tool to manage dependencies called `pipenv`. Ensure that `pipenv` is installed on the machine using pip installer: 

```
pip install --user pipenv   
```

Pipenv is a tool that provides all necessary means to create a virtual environment for a Python project. It automatically manages project packages through the `Pipfile` as the user installs or uninstalls packages. 


With pip installed we will use the following as the directory for backend code: 

```
kudos_oss 
```

Previously we installed pipenv now we will create a Python 3 virtual environment and install Flask using the following command: 

```
pipenv install flask==1.0.2
```


Python 3 provides some features like `absolute_import` and `print_function` that will be used in this project. To import them we create the following files. 

```
touch __init__.py
touch __main__.py
```

Inside the `__main__.py` file we have the following line of code: 

```python
from __future__ import absolute_import, print_function 
```


The backend for this project will need to implement the following user stories: 

* Allows an authenticated user to favorite a github open source project 
* Allows an authenticated user to unfavorite a github open source project 
* Allows an authenticated user to list all bookmarked github open source projects previously favorited 

We will be creating a classic REST API to expose endpoints which cover all the basic `CRUD (Create, Read, Update, Delete)` functionalities. By the end of this section, the backend application will handle the following HTTP calls: 

```
# For an authenticated user, fetch all favorited github open source projects 

GET /kudos

# Favorite a github open source project for an authenticated user 

POST /kudos

# Unfavorite a favorited github open source project 

DELETE /kudos/:id
```


# Defining the Python Model Schemas 

A **database schema** is a skeleton structure that represents the logical view of the entire database. It defines how the data is organized and how the relations among them are associated. It formulates all the constraints that are to be applied on the data. 

For this project the REST API will have two core schemas which are the `GithubRepoSchema` and `KudoSchema`. The `GithubRepoSchema` wil represent a Github Repository sent by the clients meanwhile the `KudoSchema` will represent the data will we are going to persist in the database. 


Next we will create a new directory which will be used to create the `app` directory with the files `schema.py`, `service.py` and `__init__.py`. 


Inside the `schema.py` file we will have the following content: 

```python 
from marshmallow import Schema, fields

class GithubRepoSchema(Schema):
    id = fields.Int(required=True)
    repo_name = fields.Str()
    full_name = fields.Str()
    language = fields.Str()
    description = fields.Str()
    repo_url = fields.URL()

class KudoSchema(GithubRepoSchema):
    user_id = fields.Email(required=True)
```

As we can see from this schema python file, the schemas are inheriting from `Schema` a package from the `marshmallow library`. Marshmallow is an `ORM/ODM/framework-agnostic library` for serializing/deserializing complex data types such as objects to and from native Python data types. 


## Overview on ORM and ODM 
ORM (Object Relational Mapper) maps the relations between data meanwhile ODM (Object Document Mapper) deals with documents. 

MySQL is an example of a relational database - you would use an ORM to translate between objects in code and the relational representation of the data. Examples of ORMs include nHibernate, Entity Framework, Dapper, etc. 

MongoDB is an example of a document database - you would use an ODM to translate between objects in code and the document representation of the data. Mandango is an example of an ODM for MongoDB. 





As previously mentioned Marshmallow is an `ORM/ODM/framework-agnostic library` for serializing/deserializing complex data types such as objects to and from native Python data types.  

Since we are using the `marshmallow` library, we have to install it using the following command: 

```
pipenv install marshmallow==2.16.3
```


# Python REST API Persistence with MongoDB

Previously we created the schemas to represent incoming request data as well as data which this application will persist in the MongoDB. In order to connect and execute queries against the database, we are going to use a library created and maintained by MongoDB called `pymonogo`. 

We are going to install the `pymongo` library as follows: 

```
pipenv install pymongo==3.7.2
```


We are going to user MongoDB for persistence in this project. There are many ways of incorporating MongoDB into a project, we previously used a method where we incorporated our projects with MLab - online MongoDB hosting. We can also have MongoDB installed on our machine or use docker to spin up a MongoDB container. For this project we are going to user `Docker` and `docker-compose` to manage a MongoDB container. 


# Docker 

Docker is a popular software for containerization - containers wrap up software and its dependencies into a standardized unit for software development that includes everything it need to run: code, runtime, system tools, and libraries. This guarantees that an application will alwasy run the same and makes collaboration as simple as sharing a container image. 

* Eliminate environment inconsistencies and the "works on my machine" problem by packaging the application, configs and dependencies into an isolated container 

* Speeds up the onboarding process and removes the need to spend time setting up development environments 



For this project `docker-compose` will manage the MongoDB container for us. 

Create a `docker-compose.yml`

```
touch docker-compose.yml
```

This file will have the following content: 

```yml
version: '3'
services:
  mongo:
    image: mongo
    restart: always
    ports:
     - "27017:27017"
    environment:
      MONGO_INITDB_ROOT_USERNAME: mongo_user
      MONGO_INITDB_ROOT_PASSWORD: mongo_secret
```

Now to spin up the container run the following: 
```
docker-compose up
```

With MongoDB up and running we are ready to work the `MongoRepository` class. It is always a good idea to have a class with just a single responsibility so the only point to deal with the back-end MongoDB application is in the `MongoRepository`. 


We start by creating a directory where all persistance related files should sit. 

```
mkdir -p app/repository
```

Then we create the file that will hold the MongoRepository class: 

```
touch app/repository/mongo.py
touch app/repository/__init__.py
```

With `pymongo` installed and MongoDB up and running, we put the following content into `app/repository/mongo.py`: 

```python
import os 
from pymongo import MongoClient 

COLLECTION_NAME = 'kudos' 

class MongoRepository(object):
 def __init__(self):
    mongo_url = os.environ.get('MONGO_URL')
    self.db = MongoClient(mongo_url).kudos

 def find_all(self, selector):
    return self.db.kudos.find(selector)

 def find(self, selector):
    return self.db.kudos.find_one(selector)

 def create(self, kudo):
    return self.db.kudos.insert_one(kudo)

 def update(self, selector, kudo):
    return self.db.kudos.replace_one(selector, kudo).modified_count

 def delete(self, selector):
    return self.db.kudos.delete_one(selector).deleted_count 
```


## Note About Python 

My previously most familiar language is Java - used it for Spring on the backend and also for Leetcode since it tends to have a lot of support for it. Now when looking at some Python functions we can see the old object-oriented concepts show up again. 

For the `MongoRepository` function/object we can see that we have the following method: 

```python 
def __init__(self):
    mongo_url = os.environ.get('MONGO_URL')
    self.db = MongoClient(mongo_url).kudos
```

The `__init__` method is the Python constructor. When we call `MongoRepository()`, Python will create an object for us and pass in any provided parameters. We can also see the `self` variable being passed into the constructor. The self variable represents the instance of the object itself (similar to `this` in Java). Most object-oriented languages pass this as a hidden parameter to the methods defined on an object - Python does not. It needs to be declared explicity so we create an instance of the `MongoRepository` class and call its methods, it will be passed automatically. For example when calling the delete function that we defined earlier, we will only pass in selector even though we defined both self and selector as arguments. 

```python 
 def delete(self, selector):
    return self.db.kudos.delete_one(selector).deleted_count 
``` 



Going back to the application, we can see that the `MongoRepository` class is pretty straightforward and provides a database connection on its initialization and saves it to and then saves it to an instance variable to be used later by the following methods: `find_all`, `find`, `create`, `update`, and `delete`. Notice that all of these methods make use of the pymongo API. 
























