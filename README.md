# MLflow Setup with PostgreSQL

## 🚀  Step 1: Install MLflow

Ensure you have Conda installed on your system.

### 1. Activate Conda Environment

```
conda activate mlflow_env
```

### 2. Install MLflow

```
pip install mlflow
```

### 3. (Optional) Install Extra Dependencies

If you plan to track models with scikit-learn:

```
pip install scikit-learn
```

### 4. Verify MLflow Installation

```
mlflow --version
```

## 📁  Step 2: Install postgresql 

### 1. Install PostgreSQL

```
sudo apt-get install postgresql postgresql-contrib postgresql-server-dev-all
```

### 2. Connect to PostgreSQL Database using CLI

```
sudo -u postgres psql
```

### 3. Create Database and User

```
CREATE DATABASE mlflow;
CREATE USER mlflow WITH ENCRYPTED PASSWORD 'mlflow';
GRANT ALL PRIVILEGES ON DATABASE mlflow TO mlflow;
```
### 4. Install psycopg2 for PostgreSQL Connection

```
sudo apt install gcc
pip install psycopg2-binary
```

### 5. Create Directory for MLflow Artifacts

```
mkdir -p /home/turbotest/mlruns
```

### 6. Run MLflow Server
```
mlflow server --backend-store-uri postgresql://mlflow:mlflow@localhost:<postgre_port>/mlflow \
  --default-artifact-root file:/home/<user>/mlruns \
  -h 0.0.0.0 -p 5000
```
Example:
```
mlflow server --backend-store-uri postgresql://mlflow:mlflow@localhost:5433/mlflow \
  --default-artifact-root file:/home/turbotest/mlruns \
  -h 0.0.0.0 -p 5000
```
### 7. Check MLflow Server

Open your browser and go to:
```
http://localhost:5000
```

## 🏃 Step 3:  Make mlflow run automatically when the server is on or fail

Setting Up MLflow as a Systemd Service

### 1. Create a Systemd Service File
```
sudo vim /etc/systemd/system/mlflow.service
```
### 2. Add the Following Configuration
```
[Unit]
Description=MLflow Tracking Server
After=network.target postgresql.service

[Service]
User=turbotest
Group=turbotest
WorkingDirectory=/home/turbotest
Environment="PATH=/home/turbotest/miniconda3/envs/mlflow_env/bin"
ExecStart=/home/turbotest/miniconda3/envs/mlflow_env/bin/mlflow server \
  --backend-store-uri postgresql://mlflow:mlflow@localhost:5433/mlflow \
  --default-artifact-root file:/home/turbotest/mlruns \
  -h 0.0.0.0 -p 5000 \
  --gunicorn-opts "--timeout 120"
Restart=always
RestartSec=10
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
```
### 3. Reload Systemd and Enable the Service

After saving the file, reload the systemd daemon:
```
sudo systemctl daemon-reload
```
Enable the service to start at boot:
```
sudo systemctl enable mlflow
```
### 4. Start and Check the Service

Start the MLflow service:
```
sudo systemctl start mlflow
```
Check its status with:
```
sudo systemctl status mlflow
```
**We are Done with Setting up MLflow**

## 📊 Step 4:  Getting Started


### 1. Run a simple code using an example from the MLflow epo to see if this works and save it in mlruns with main.py name:
```
https://github.com/mlflow/mlflow/blob/master/examples/sklearn_elasticnet_wine/train.py
```

### 2. Also, add this code in the main.py:
```
mlflow.set_tracking_uri("http://localhost:5000")  # Replace with your MLflow server URI
```
### 3. Run the code with:
```
python main.py
```
### 4. Check the MLflow cluster again at:
```
http://localhost:5000
```
