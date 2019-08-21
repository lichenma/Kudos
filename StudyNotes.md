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









