## Acme project directories schema:

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
│   │
│   └───data 
│       │   (x)
│   
└───env
│   │   ...
│
└───dockerfile
    │   python.dockerfile

```

#### All terminal commmands should be executed from the main project directory 'acme_project'. 

# Virtual Enviroment configuration
```
python3 -m venv ./env
source ./env/bin/activate
(env) pip install -r requirements.txt
(env) deactivate 
python3 -m ipykernel install --user --name=hypeq1
```

# MySQL

## Execute mysql container directly from mysql latest image version
```
!docker run --rm --name mysql_app -d -v $(pwd)/db/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=hype_psd mysql
```

## Open bash in mysql container
```
docker exec -it mysql_app /bin/bash
```

## Create database 'db' in mysql 
```
docker exec -i mysql_app mysql -uroot -phype_psd < ./db/create_db.sql 
```

# Python

## Build python3 app image
``` 
docker build -t python3-img -f $(pwd)/dockerfile/python.dockerfile .
```

- OPENSSL_CONF=/path/to/custom/openssl.cnf is a custom .cnf file to access the IBGE api. 
- https://stackoverflow.com/questions/71603314/ssl-error-unsafe-legacy-renegotiation-disabled
- maybe -p 9001:9001 --link mysql_app

## Run python containerized application w/ symbolic link to mysql container (maybe -p too)
```
sudo docker run -e OPENSSL_CONF=$(pwd)/app/openssl.cnf -it --link mysql_app --rm --name python_app -v $(pwd)/app:/usr/src/myapp -w /usr/src/myapp python3-img python app_hype.py
```



