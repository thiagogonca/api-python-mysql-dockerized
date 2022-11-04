# Acme Project

## Tree-based Directories & file structures 

```
acme_project
│   README.md
│   project_draft.ipynb    
│   requirements.txt
│
└───app
│   │   app_hype.py
│
└───db
│   │   query_hype.sql
│   │   create_db.sql
│   └───data (dockerized)
│   
└───env_hypeq1
│   │   ...
│
└───dockerfile
    │   python.dockerfile

```

## All terminal commmands should be executed from the root project directory 'acme_project'. 

## Virtual Enviroment configuration (for debugging purposes)
```
python3 -m venv ./env_hypeq1
source ./env_hypeq1/bin/activate
(env_hypeq1) pip install -r requirements.txt
```
# Step by step to run the application

## MySQL

### Execute mysql container directly from mysql latest image version (dont't forget to initialize docker first)
```
docker run --rm --name mysql_app -d -v $(pwd)/db/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=hype_psd mysql
```

### Create database 'db' in mysql from create_db.sql
```
docker exec -i mysql_app mysql -uroot -phype_psd < ./db/create_db.sql 
```

### Enter inside mysql dbms from mysql container (optional)
```
docker exec -it mysql_app mysql -uroot -phype_psd
```

## Python

- openssl.cnf is a custom .cnf file to access the IBGE api. 
- https://stackoverflow.com/questions/71603314/ssl-error-unsafe-legacy-renegotiation-disabled

### Build python3 app image
``` 
docker build -t python3-img -f $(pwd)/dockerfile/python.dockerfile .
```

### Run python containerized application w/ symbolic link to mysql container
```
docker run -e OPENSSL_CONF=$(pwd)/app/openssl.cnf -it --link mysql_app --rm --name python_app -v $(pwd)/app:/usr/src/myapp -w /usr/src/myapp python3-img 
```
