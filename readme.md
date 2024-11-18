```sql
USE ROLE accountadmin;
CREATE OR REPLACE WAREHOUSE ML_HOL_WH; --by default, this creates an XS Standard Warehouse
CREATE OR REPLACE DATABASE ML_HOL_DB;
CREATE OR REPLACE SCHEMA ML_HOL_SCHEMA;
CREATE OR REPLACE STAGE ML_HOL_ASSETS; --to store model assets

-- create csv format
CREATE FILE FORMAT IF NOT EXISTS ML_HOL_DB.ML_HOL_SCHEMA.CSVFORMAT 
    SKIP_HEADER = 1 
    TYPE = 'CSV';

-- create external stage with the csv format to stage the diamonds dataset
CREATE STAGE IF NOT EXISTS ML_HOL_DB.ML_HOL_SCHEMA.DIAMONDS_ASSETS 
    FILE_FORMAT = ML_HOL_DB.ML_HOL_SCHEMA.CSVFORMAT 
    URL = 's3://sfquickstarts/intro-to-machine-learning-with-snowpark-ml-for-python/diamonds.csv';
    -- https://sfquickstarts.s3.us-west-1.amazonaws.com/intro-to-machine-learning-with-snowpark-ml-for-python/diamonds.csv

LS @DIAMONDS_ASSETS;


CREATE OR REPLACE API INTEGRATION git_api_integration
  API_PROVIDER = git_https_api
  API_ALLOWED_PREFIXES = ('https://github.com')
  ENABLED = TRUE;


CREATE OR REPLACE GIT REPOSITORY ML_HOL_GIT_REPO
  API_INTEGRATION = git_api_integration
  ORIGIN = 'https://github.com/Snowflake-Labs/sfguide-intro-to-machine-learning-with-snowflake-ml-for-python.git';

ls '@ml_hol_git_repo/branches/main';

create or replace notebook nb_0_start_here
from '@ml_hol_git_repo/branches/main/notebooks/'
main_file='0_start_here.ipynb'
query_warehouse = ML_HOL_WH;

create or replace notebook nb_1_sf_nb_snowflake_ml_feature_transformations
from '@ml_hol_git_repo/branches/main/notebooks/'
main_file='1_sf_nb_snowflake_ml_feature_transformations.ipynb'
query_warehouse = ML_HOL_WH;

create or replace notebook nb_2_sf_nb_snowflake_ml_model_training_inference
from '@ml_hol_git_repo/branches/main/notebooks/'
main_file='2_sf_nb_snowflake_ml_model_training_inference.ipynb'
query_warehouse = ML_HOL_WH;
```
