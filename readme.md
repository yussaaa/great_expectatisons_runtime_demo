# Great expectations setup with Iowa liquor data

The use of great expectations(abbrevate as ge in the rest of the document), could be split into 4 stpes. These steps will be discussed below in details.

## 1. Setup: package setup and data context initialization
``` python 
pip install great_expectations

great_expectations init
```

* Note: If need to use the offline version of the package, please use the following command 
``` pip install git+https://github.com/StatCan/great_expectations.git@feature/offline-data-docs ```

## 2. Connect to data

### a) Related terms: 
1. Data Context: primary entry point to ge
2. Data Connector: Setup the configuration for data connection to data source 
    * RuntimeDataConnector: accessing **in-memory** dataframe using *RuntimBatchRequest*
    * InferredAssetFilesystemDataConnector: RegEX on filename patterns
    * ConfiguredAssetFilesystemDataConnector: Fine tuned on the 
3. Data Source: API access/config to the data assets 
4. Data Asset: The actual data object
------- 
5. Batch: Records within Data Asset
6. Batch Request: Specifying what data asset to use in the config

### b) Stpes to steup 
```python
great_expectations datasource new

1. file systems 
1. Pandas 
./data (ex)
```
And follow the instructions

### c) Note on various data connector: 
In the great_expectations.ymal file, the default datasource config will look like the following: 
```
datasource_yaml = f"""
name: getting_started_datasource
class_name: Datasource
execution_engine:
  class_name: PandasExecutionEngine
data_connectors:
    default_inferred_data_connector_name:
        class_name: InferredAssetFilesystemDataConnector
        base_directory: ../data/
        default_regex:
          group_names:
            - data_asset_name
          pattern: (.*)
    default_runtime_data_connector_name:
        class_name: RuntimeDataConnector
        assets:
            my_runtime_asset_name:
              batch_identifiers:
                - runtime_batch_identifier_name
"""
```

**Two data connectors** are specified in the config file, however we are actually only using the default_runtime_data_connector_name.<br>

[runtime batch request setup github notebook, see line 62-71](https://github.com/great-expectations/great_expectations/blob/develop/tests/integration/docusaurus/connecting_to_your_data/how_to_create_a_batch_of_data_from_an_in_memory_pandas_dataframe.py)


## 3. Create Expectations 
### a) CLI commands
```
great_expectations suite new
```
### b) Quickly explor expecttions in a notebook
[How to quickly explore Expectations in a notebook](https://docs.greatexpectations.io/docs/guides/miscellaneous/how_to_quickly_explore_expectations_in_a_notebook#5-save-your-expectation-suite)
See suite_from_notebook.ipynb 

### c) Three ways to create a suite
1. From domin knowwledge 
2. Using sample batch of data to create a validator (interactive mode)
   * Add expectations by calling specific expectation methods on the validator object. They all begin with .expect_
   * After exploring, save the validator to a checkpoint
3. Automatically with a profiler
    * Profiler creates a starting point for quickly generating Expectations

## 4. Validate data

### a) Related terms: 
1. Data Docs: Final html expectaions report 
2. Checkpoint: Saved object for late comparisions. The YAML is saved under ./great_expectations/checkpoints/XXX.ymal
    * You can include multiple -batch_request in the validatios section. This setup could include multiple expectation_suits, or multiple data assets. 
    * Make sure the **data asset name** matches its **data connector type**
    * [In-memory df batch request reference github notebook](https://github.com/great-expectations/great_expectations/blob/develop/tests/integration/docusaurus/validation/checkpoints/how_to_pass_an_in_memory_dataframe_to_a_checkpoint.py)
### b) CLI commands:
```
great_expectations checkpoint new getting_started_checkpoint
```

### c) Notes: 
The final data docs report is saved under ```great_expectations/uncommitted/data_docs```

## 6. References: 
* [Offical document](https://docs.greatexpectations.io/docs/tutorials/getting_started/tutorial_overview)

