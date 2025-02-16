# Introduction
Conda is a command-line tool for managing packages and environments. In TM351, it is used to set up an environment that allows experimentation with Jupyter Notebooks.

# Getting Started

## 1. Download & Install Miniforge
Miniforge is a wrapper to easily setup Conda. [Click Here to Download](https://conda-forge.org/download/). Follow instructions to install into your OS. Now you can run Conda via the Miniforge prompt in the Start menu on Windows, or via `~/miniforge3/bin/conda` on Mac & Linux.

## 2. Clone Repository
Clone or download this repository.
```
git clone https://github.com/amkige/tm351-conda.git
```

## 3. Create TM351 Environment
1. Run Conda using the Miniforge prompt from the Start menu on Windows, or via `conda` command on Mac and Linux. Ensure (base) appears in the command prompt, indicating the base environment is active like so:
    ```
    (base)>
    ```

2. Update base packages

    ```
    conda update -n base -c conda-forge conda
    ```
3. Navigate to the `tm351-conda` directory

    ```
    cd tm351-conda
    ```

4. Create `tm351` environment from the `environment-win.yml` or `environment-unix.yml` file for windows or Mac/Linux respectively.

    ```
    # Windows
    conda env create -f environment-win.yml

    # Mac & Linux
    conda env create -f environment-unix.yml
    ```

5. Activate `tm351` environment

    ```
    conda activate tm351
    ```

## 4. Setup PostgreSQL
1. Activate `tm351` environment

    ```
    conda activate tm351
    ```

2. Initialize a database cluster (Note this will create pg_cluster directory to store data)

    ```
    initdb -D pg_cluster
    ```

3. Start the server

    ```
    pg_ctl -D pg_cluster -l pg_logs start
    ```

4. Create a user and set a password (set the password to dbpass)

    ```
    createuser --pwprompt dbuser
    ```

5. Create a database

    ```
    createdb --owner=dbuser tm351db
    ```

## 5. Setup MongoDB
1. Activate `tm351` environment

    ```
    conda activate tm351
    ```
    
2. Install mongodb
    ```
    conda install mongodb
    ```
3. Create a directory to store database data

    ```
    mkdir monogodb_data
    ```

4. Start the server

    ```
    mongod --dbpath monogodb_data --fork --logpath mongodb_logs
    ```

5. Import Accidents Database

    ```
    mongorestore --drop --gzip --archive=./accidents_database.gz
    ```

## 6. Start Jupyter Labs

    jupyter lab --notebook-dir=./Notebooks

> [!NOTE]
> Start the PostgreSQL or MongoDB server before running notebooks that require them
> ```
> # PostgreSQL
> pg_ctl -D pg_cluster -l pg_log start
> 
> # MongoDB
> mongod --dbpath monogodb_data --fork --logpath mongodb_logs
> ```
> Stop the servers after finishing
> ```
> # PostgreSQL
> pg_ctl -D pg_cluster stop
> 
> # MongoDB
> mongod --dbpath monogodb_data --shutdown
> ```