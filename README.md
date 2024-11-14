# Steps to Install Packages in Anaconda
## Using Anaconda Navigator
    1. Open Anaconda Navigator.
    2. Go to **Environments**.
    3. Set filter to **Not Installed**.
    4. Check Package and Click **Apply** to Install.

## Using Anaconda Prompt
    1. Open Anaconda Prompt.
    2. Run `conda install PACKAGE_NAME`.

# Install Required Packages
The following packages are mandatory for everything to work correctly.
- postgresql
- psycopg2

# Run Linux commands [Windows]
Install `m2-base` package through **Anaconda Navigator** or **Anaconda Prompt**.

# Setup PostgreSQL
1. Open Anaconda Prompt.
2. Install postgresql: `conda install postgresql`.
3. Initialize the database: `initdb -D db`.
4. Start PostgreSQL Server: `pg_ctl -D db -l logfile start`.
5. Create a User and set the Password: `createuser --encrypted --pwprompt USERNAME`.
6. Create a Database `createdb --owner=USERNAME DATABASE_NAME`.

# Fix Notebooks Issues

These notebooks are almost 9 years old, they are ancient, since then, dataset URLs have changed, and libraries and their methods have been deprecated.

Here, I'll document the necessary changes to fix some of the issues I've encountered.

## Notebook 2.2.1

- `ignore` is deprecated. Instead, catch exceptions explicitly or use `coerce` to convert unparsable values to NaN.

    
    ```python
    # Old
    pd.to_numeric(df['Amount'], errors='ignore')

    # New
    pd.to_numeric(df['Amount'], errors='coerce')
    ```

## Notebook 2.2.2

- The dataset URL is no longer active. Use the new URL, which contains the same data.

    ```python
    # old
    url = "http://data.ordnancesurvey.co.uk/datasets/os-linked-data/apis/reconciliation?query=MK7&type=http%3A%2F%2Fdata.ordnancesurvey.co.uk%2Fontology%2Fpostcode%2FPostcodeSector&type_strict=any&limit=10"

    # new
    url = "https://gist.githubusercontent.com/amkige/2695619be7bed1646d087d92fedc136b/raw/f2d8c2a62daa1ca668eb3572a81f91d3bdc12fe5/ordanance-survey-dataset.json"
    ```

- `json_normalize` is obsolete, use `pd.json_normalize` instead.

    ```python
    # old
    from pandas.io.json import json_normalize
    json_normalize(data['result'])

    # new
    pd.json_normalize(data['result'])
    ```

- `.ix` is obsolete, use `.loc` instead.

    ```python
    # old
    pd.io.json.read_json('http://www.bbc.co.uk/programmes/p01ztvcp.json').ix[rows]

    # new
    pd.io.json.read_json('http://www.bbc.co.uk/programmes/p01ztvcp.json').loc[rows]
    ```

## Notebook 3.4

- Make sure `numpy` library is imported and aliased as `np`.

    ```python
    import numpy as np
    ```

- Make sure to update PostgresSQL connection URI and set username, password and database to the ones you created.

    ```python
    %sql postgresql://USERNAME:PASSWORD@localhost:5432/DATABASE_NAME
    ```

- Creating a table with SQL magic may raise a `KeyError: 'DEFAULT'` error (see [this Stack Overflow answer](https://stackoverflow.com/a/79155603)).\
A workaround for this issue is to use:


    ```python
    %config SqlMagic.style = '_DEPRECATED_DEFAULT'
    ```

## Notebook 4.1
- `aggfunc=np.x` may not work in the future, use `aggfunc="x"` instead.

    ```python
    # old
    df.pivot_table(index=['Directorate', 'Capital or Revenue'], aggfunc=np.sum)

    # new
    df.pivot_table(index=['Directorate', 'Capital or Revenue'], aggfunc="sum")
    ```
- Aggregate Functions like `sum`, `median` default to `numeric_only=False` now.

    ```python
    # old 
    df.pivot_table(index=['Directorate'], columns=['Capital or Revenue'], aggfunc=[np.sum, np.median])

    # new
    df.pivot_table(index=['Directorate'], columns=['Capital or Revenue'], values=df.select_dtypes(include='number').columns, aggfunc=["sum", "median"])