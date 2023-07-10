# Deploy Airflow to Heroku HowTo

1. Install Airflow locally in Python3 venv

```
python3 -m venv .venv
source .venv/bin/activate
pip install "apache-airflow[postgres, password]"
pip install "cryptography"
```

2. Fix pydantic v2.x.x bug with Airflow v2.6.2

```
pip uninstall pydantic
pip install pydantic==1.10.10
pip freeze > requirements.txt
```

3. Create files for Heroku deployment

```
# Define Python buildpack's version
echo "python-3.9.16" > runtime.txt
# Define Procfile to launch Airflow server
echo "web: airflow webserver --port \$PORT" > Procfile
```

4. Create `airflow-heroku` Airflow repository on Github

```
git init .
git add --all
git commit -m "First commit"
```

5. Setup Heroku app

Login to Heroku Dashboard, create a new app with PostgreSQL db add-on, link app to `airflow-heroku` Github repository.

6. Define environnment variables on Heroku

```
# Generate Fernet key to enable passwords encryption
heroku config:set AIRFLOW__CORE__FERNET_KEY=`dd if=/dev/urandom bs=32 count=1 2>/dev/null | openssl base64`
heroku config:set AIRFLOW_HOME=/app
```

7. Init Airflow db

```
heroku run \bin\bash
airflow initdb
```

Airflow db should init successfully on Heroku logs, and Airflow server should start when application relaunches.
