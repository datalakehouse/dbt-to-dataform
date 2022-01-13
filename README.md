# DBT to Dataform Conversion

This jupyter process converts a project written in DBT by a Dataform project.
[In this spreadsheet](https://docs.google.com/spreadsheets/d/1q96HottHJaEC9vZ0NPrwLsoxVpnRE7fLGz5zI-y0mM0/edit#gid=0), you can see the details about the objects that are converted by the python code, and some important notes about possible limitations, as well as the roadmap for future implementations.

## Process to follow before running notebook

* Make sure you already have dataform installed on your computer; If it's not, you follow use [this walkthrough.](https://docs.google.com/document/d/1XO6TBx3uXLtpa8jvVBOoQrFvkm3Gg4QQ_MgcEQGeXns/edit)
* Make sure you have DBT's source repository on your local machine;
* Clone this repository on the same path as you have your DBT Project 
    * gh repo clone datalakehouse/dbt-to-dataform
* Copy .df_credentials.json file that you had generated on dataform configuration to the same path; The path structure should be like below;
    * dbt_project/
    * .df_credentials.json
    * dbt-to-dataform/notebook
* Make sure you have Python, Juptyter notebook or Hub installed on your machine;
* [Read the spreadsheet](https://https://docs.google.com/spreadsheets/d/1q96HottHJaEC9vZ0NPrwLsoxVpnRE7fLGz5zI-y0mM0/edit#gid=0) to make sure that each part of your code will be converted as expected;

## Begin the process

### Start Jupyter

Execute **jupyter-notebook** command on your CLI to start jupyter notebook.

After starting the jupyter notebook on your local machine, navigate to the webpage. This is typically, http://localhost:8888/

Navigate until **dbt_dataform_converter.ipynb** file.

### Input variables

On this part of the code (image), insert the variables as requested.
* dbt_source_project_path: The path of your source dbt project;
* dataform_root_path: The target dataform path to be generated;
* target_schema: The name of the schema to be created by Dataform on snowflake;
* dlh_timestamp_field: If your code has SCD Snapshot files, Dataform requires to inform a timestamp field to be checked when generating snapshot. Must be a field on your model that tracks the last update datetime for each record;

![](https://i.imgur.com/Bo8p9GW.png)

### Running the python code

Run each cell of **dbt_dataform_converter.ipynb** file.

On the last cell, you must have a return close to this.

![](https://i.imgur.com/5MoXfZa.png)

### Running dataform project

Make sure you have [read the spreadsheet](https://https://docs.google.com/spreadsheets/d/1q96HottHJaEC9vZ0NPrwLsoxVpnRE7fLGz5zI-y0mM0/edit#gid=0) to understand the limitations of the converter based on your current DBT code.

On the case of the test I've made, based on dlh_square_analytics project, below are the changes that I needed to do before running dataform's code.

* Square analytics project, uses a full_name macro. It was required to be rewritten on dataform. Write your macro in a .JS file and put that file inside includes folder.

![](https://i.imgur.com/eIifhan.png)

* Add the name of the macro file before the name of the macro

![](https://i.imgur.com/LcmQlSt.png)

* Execute dataform compile comand to make sure anything will break

![](https://i.imgur.com/cduMJVs.png)

* Change default schema on dataform.json file

![](https://i.imgur.com/zSBqO0I.png)

* Execute dataform run

![](https://i.imgur.com/U00c6ew.png)


## Output

The output dataform will be generated on the path contained on **dataform_root_path** variable.

When the code runs, it will check if this directory already exists. If it does not exists, it will be created, otherwise, it will be deleted and created again.

Below, are the functions that will be ran in sequence by **dbt_dataform_converter** function.

#### dataform_install_configuration
#
1. delete target repository if exists;
2. dataform init new repository;
3. copy .df_credentials file to new repository
4. edit packages.json file with target dataform version, and adding dataform-scd package;
5. runs dataform install to setup target version and scd package;

#### create_sqlx_json_source_file
#
1. gets all yml files that contains sources on models folder of dbt source project;
2. generate one file for each source table contained on the yml files on definitions/sources on dataform project;
3. The name of the file will follow this pattern: **schema**_**table**.sqlx

#### create_sqlx_models_files
# 
1. gets all .sql files on dbt's models repository;
2. copy all files to target dataform's definitions repository, replacing the extension to .sql;
3. replace header with dataform's syntax using replace and regex substitution functions;
4. replace syntax patterns on dbt project to dataform's;
5. remove unsupported DBT's config header features;
6. replace syntax pattern of incremental models macros on dataform;

#### create_slx_snapshot_files
#
1. gets all .sql files on dbt's snapshots repository;
2. copy all files to target dataform's definitions/snapthots repository, replacing the extension to .js;
3. gets the name of the table used on FROM clause of the snapshot file;
4. replace file for the dataform's scd JS pattern;

#### dataform_assertions_documentation

1. gets all .yml test and schema definition files on DBT's model repository;
2. gets unique, not_null tests and the corresponding tables and columns;
3. gets descriptions on tables and/or columns presents on yml files;
4. create a python dictionary with those tests and descriptions;
5. write assertions (tests) and descriptions inside each model already present on dataform's definitions folder

