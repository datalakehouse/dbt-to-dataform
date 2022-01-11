# DBT to Dataform Conversion

This jupyter process converts a project written in DBT by a Dataform project.
[In this spreadsheet](https://docs.google.com/spreadsheets/d/1q96HottHJaEC9vZ0NPrwLsoxVpnRE7fLGz5zI-y0mM0/edit#gid=0), you can see the details about the objects that are converted by the python code, and some important notes about possible limitations, as well as the roadmap for future implementations.

## Process to follow before running notebook

* Make sure you already have dataform installed on your computer; If it's not, you follow use [this walkthrough.]
(https://docs.google.com/document/d/1XO6TBx3uXLtpa8jvVBOoQrFvkm3Gg4QQ_MgcEQGeXns/edit)
* Make sure you have the repository of the DBT source project to be converted on your local machine;
* Clone this repository on the same path as you have your DBT Project 
    * gh repo clone datalakehouse/dbt-to-dataform
* Copy your .df_credentials.json file that you generated on dataform configuration to the same path; The path structure should be like below;
    * dbt_project/
    * .df_credentials.json
    * dbt-to-dataform/notebook
* Make sure you have Python, Juptyter notebook or Hub installed on your machine;
* [Read the spreadsheet](https://https://docs.google.com/spreadsheets/d/1q96HottHJaEC9vZ0NPrwLsoxVpnRE7fLGz5zI-y0mM0/edit#gid=0) to make sure that each part of your code will be converted as expected;

## Start Jupyter

Execute **jupyter-notebook** command on your CLI to start jupyer notebook.

## Input variables

On this part of the code (image), insert the variables as requested.
* dbt_source_project_path: The path of your source dbt project;
* dataform_root_path: The target dataform path to be generated;
* target_schema: The name of the schema to be created by Dataform on snowflake;
* dlh_timestamp_filed: If your code has SCD Snapshot files, Dataform requires to inform a timestamp field to be checked when generating snapshot. Must be a field on your model that tracks the last update datetime for each record;

![](https://i.imgur.com/Bo8p9GW.png)


