# Steps to Install Packages in Anaconda

### Using Anaconda Navigator
1. Open Anaconda Navigator
2. Go to **Environments**
3. Set filter to **Not Installed**
4. Check Package and Click **Apply** to Install

### Using Anaconda Prompt
1. Open Anaconda Prompt
2. Run `conda install PACKAGE_NAME`

# Required Packages for TM351 Notebooks
The following packages are mandatory for everything to work correctly.
- postgresql
- psycopg2
- folium
- pandasql
- xlrd
- conda-forge::fastkml
- jupysql
- lxml
- matplotlib
- seaborn
- openpyxl
- mongodb
- mongo-tools
- pymongo
- scipy
- scikit-learn
- conda-forge::rdflib
- conda-forge::sparqlwrapper

# Run Linux Commands on Windows
Install `m2-base` package through **Anaconda Navigator** or **Anaconda Prompt**.

# Setup PostgreSQL
1. Open Anaconda Prompt
2. Install PostgreSQL `conda install postgresql`
3. Initialize a database cluster `initdb -D pg_cluster`
4. Start the server `pg_ctl -D pg_cluster start`
5. Create a user and set a password `createuser --pwprompt dbuser` (set password to dbpass)
6. Create a database `createdb --owner=dbuser tm351db`

# Setup MongoDB
1. Open Anaconda Prompt
2. Install mongodb `conda install mongodb`
3. Create a directory to store database data
4. Start Mongo Daemon `mongod --dbpath /path/to/store/data`

<br>

---

<br>

# TM351 Notebooks Issues

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

## Notebook 2.2.3
- change `pd.read_excel` parameter `sheetname` to `sheet_name`.

    ```python
        # old
        pd.read_excel('data/tfl-buses-type.xls', sheetname='Data')[:3]

        # new
        pd.read_excel('data/tfl-buses-type.xls', sheet_name='Data')[:3]
    ```

## Notebook 3.1
- Declare Regex using raw string.

    ```python
        # old
        "...^\d..."

        # new
        r"...^\d..."
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
- Aggregate Functions like `sum`, `median` now default to `numeric_only=False`, Thus:
    - Making it necessary to specify the columns to aggregate.

        ```python
        # old 
        df.pivot_table(index=['Directorate'], aggfunc=np.sum)

        # new
        df.pivot_table(index=['Directorate'], values=["Amount"], aggfunc="sum")
        ```

    - The `ASIDE` and its exercise should be removed.

- Since numpy functions are no longer used, the `Something to watch out for` section should be removed.

## Notebook 4.3
- Change `subplots='True'` to `subplots=True`.
- Nested renamer is no longer supported.

    ```python
    # old
    groupeddata['Mark'].agg({'top mark':'max', 
                            'low mark':'min',
                            'average mark':'mean'})
    # new
    groupeddata['Mark'].agg(top_mark='max', 
                            low_mark='min',
                            average_mark='mean')
    ```
- Use `is_numeric_dtype` method to check if an object is a number in the `make_mean_3000_if_below` transform function.

## Notebook 4.7
- `df.pivot()` no longer accpets arguments by position.

    ```python
    # old
    df_wide = df_long.pivot('expense types', 'directorates', 'total')

    # new
    df_wide = df_long.pivot(index='expense types', columns='directorates', values='total')
    ```

## Notebook 5.2
- Change `map.x` to `folium.x().add_to(map)`.
- Change `render(path=)` to `save(outfile=)`.
- Choropleth method argument changes.

## Notebook 14.1
- `find().count()` is obsolete use `count_documents()` instead.

    ```python
    # old
    tc.find({'name': 'Peter'}).count()

    # new
    tc.count_documents({'name': 'Peter'})
    ```

## Notebook 15.3
- DataFrame.drop only takes two positional arguments

    ```python
    # old
    results_df.drop('Pedal cycles', 1)
    
    # new
    results_df.drop('Pedal cycles', axis=1)

## Notebook 15.4
- geoNear is obsolete, use $geoNear aggregation instead.

    ```python
    # old
    nearest_road_result = db.command(SON([('geoNear', 'roads'), 
                ('near', a['loc']),                      
                ('spherical', True),
                ('query', {'RCat': 'TM'}),
                ('limit', 1)]))

    print(nearest_road_result['results'][0]['obj']['CP'], 
      nearest_road_result['results'][0]['obj']['ONS LA Name'],
      nearest_road_result['results'][0]['obj']['Road'])
      
    # new
    nearest_road_result = roads.aggregate([
        {
            '$geoNear': {
                'distanceField': 'dist',
                'near': a['loc'],
                'spherical': True,
                'query': { 'RCat': 'TM' },
            }
        },
        {
            '$limit': 1
        }
    ])

    for road in nearest_road_result:
        print(road['CP'], road['ONS LA Name'], road['Road'])
    ```

## Notebook 16.3
- Map-Reduce is [deprecated in MongoDB](https://www.mongodb.com/docs/manual/core/map-reduce/) and is removed from [PyMongo since version 4](https://pymongo.readthedocs.io/en/stable/migrate-to-pymongo4.html#collection-map-reduce-and-collection-inline-map-reduce-are-removed).

## Notebook 20.2
- `KNeighborsClassifier.predict` expects a 2D container not a series.

    ```python
    # old
    return myClassifier.predict(trainingData_df.loc[ix])[0]

    # new
    return myClassifier.predict(trainingData_df.loc[[ix]])[0]
    ```

## Notebook 23.1
- As of [PostgreSQL 15](https://www.postgresql.org/about/news/postgresql-15-released-2526/#:~:text=PostgreSQL%2015%20also%20revokes%20the%20CREATE%20permission%20from%20all%20users%20except%20a%20database%20owner%20from%20the%20public%20(or%20default)%20schema.), privileges to alter the schema is only granted to database owner. Must grant privileges manually.
    
    ```sql
    GRANT ALL ON SCHEMA public TO MY_NEW_USER;
    ```