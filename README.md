# Introduction
Conda is a command-line tool for managing packages and environments. In TM351, it is used to set up an environment that allows experimentation with Jupyter Notebooks.

# Getting Started

## 1. Clone Repository
Clone or download this repository.
```
git clone https://github.com/amkige/tm351-conda.git
```

## 2. Download & Install Miniforge
Miniforge is a wrapper to easily setup conda. [Click Here to Download](https://conda-forge.org/download/). Follow instructions to install into your OS. Now you can use conda via the Miniforge prompt in the start menu on Windows, or `~/miniforge3/bin/conda` on Mac & Linux.

## 3. Create TM351 Environment
1. Update base packages

    ```
    conda update -n base -c conda-forge conda
    ```

2. Create `tm351` environment from the `environment.yml` file

    ```
    # Windows
    conda env create -f environment-win.yml

    # Mac & Linux
    conda env create -f environment-unix.yml
    ```

3. Activate `tm351` environment

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
    pg_ctl -D pg_cluster start
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
    mongod --dbpath monogodb_data
    ```