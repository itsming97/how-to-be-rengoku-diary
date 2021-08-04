# Scaffold a new Expectation Suite (Experimental)
This process helps you avoid writing lots of boilerplate when authoring suites by allowing you to select columns and other factors that you care about and letting a profiler write some candidate expectations for you to adjust.

**Expectation Suite Name**: `taxi.demo`

We'd love it if you'd **reach out to us on** the [**Great Expectations Slack Channel**](https://greatexpectations.io/slack)!


```python
import great_expectations as ge
from great_expectations.checkpoint import LegacyCheckpoint
from great_expectations.profile.user_configurable_profiler import UserConfigurableProfiler
from great_expectations.data_context.types.resource_identifiers import ValidationResultIdentifier

context = ge.data_context.DataContext()

expectation_suite_name = "taxi.demo"

# Wipe the suite clean to prevent unwanted expectations in the batch
suite = context.create_expectation_suite(expectation_suite_name, overwrite_existing=True)

batch_kwargs = {'path': 'D:\\gathi\\great_expecatation\\ge_tutorials\\great_expectations\\..\\data\\yellow_tripdata_sample_2019-01.csv', 'datasource': 'data__dir', 'data_asset_name': 'yellow_tripdata_sample_2019-01'}
batch = context.get_batch(batch_kwargs, suite)
batch.head()
```

    c:\python\python39\lib\site-packages\great_expectations\expectations\metrics\column_aggregate_metrics\column_quantile_values.py:3: DeprecationWarning: Using or importing the ABCs from 'collections' instead of from 'collections.abc' is deprecated since Python 3.3, and in 3.10 it will stop working
      from collections import Iterable
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendor_id</th>
      <th>pickup_datetime</th>
      <th>dropoff_datetime</th>
      <th>passenger_count</th>
      <th>trip_distance</th>
      <th>rate_code_id</th>
      <th>store_and_fwd_flag</th>
      <th>pickup_location_id</th>
      <th>dropoff_location_id</th>
      <th>payment_type</th>
      <th>fare_amount</th>
      <th>extra</th>
      <th>mta_tax</th>
      <th>tip_amount</th>
      <th>tolls_amount</th>
      <th>improvement_surcharge</th>
      <th>total_amount</th>
      <th>congestion_surcharge</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>2019-01-15 03:36:12</td>
      <td>2019-01-15 03:42:19</td>
      <td>1</td>
      <td>1.0</td>
      <td>1</td>
      <td>N</td>
      <td>230</td>
      <td>48</td>
      <td>1</td>
      <td>6.5</td>
      <td>0.5</td>
      <td>0.5</td>
      <td>1.95</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>9.75</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>2019-01-25 18:20:32</td>
      <td>2019-01-25 18:26:55</td>
      <td>1</td>
      <td>0.8</td>
      <td>1</td>
      <td>N</td>
      <td>112</td>
      <td>112</td>
      <td>1</td>
      <td>6.0</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>1.55</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>9.35</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>2019-01-05 06:47:31</td>
      <td>2019-01-05 06:52:19</td>
      <td>1</td>
      <td>1.1</td>
      <td>1</td>
      <td>N</td>
      <td>107</td>
      <td>4</td>
      <td>2</td>
      <td>6.0</td>
      <td>0.0</td>
      <td>0.5</td>
      <td>0.00</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>6.80</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>2019-01-09 15:08:02</td>
      <td>2019-01-09 15:20:17</td>
      <td>1</td>
      <td>2.5</td>
      <td>1</td>
      <td>N</td>
      <td>143</td>
      <td>158</td>
      <td>1</td>
      <td>11.0</td>
      <td>0.0</td>
      <td>0.5</td>
      <td>3.00</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>14.80</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>2019-01-25 18:49:51</td>
      <td>2019-01-25 18:56:44</td>
      <td>1</td>
      <td>0.8</td>
      <td>1</td>
      <td>N</td>
      <td>246</td>
      <td>90</td>
      <td>1</td>
      <td>6.5</td>
      <td>1.0</td>
      <td>0.5</td>
      <td>1.65</td>
      <td>0.0</td>
      <td>0.3</td>
      <td>9.95</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>



## Select the columns on which you would like to scaffold expectations and those which you would like to ignore.

Great Expectations will choose which expectations might make sense for a column based on the **data type** and **cardinality** of the data in each selected column.

Simply comment out columns that are important and should be included. You can select multiple lines and
use a jupyter keyboard shortcut to toggle each line: **Linux/Windows**:
`Ctrl-/`, **macOS**: `Cmd-/`


```python
ignored_columns = [
    'vendor_id',
    'pickup_datetime',
    'dropoff_datetime',
    #'passenger_count',
    'trip_distance',
    'rate_code_id',
    'store_and_fwd_flag',
    'pickup_location_id',
    'dropoff_location_id',
    'payment_type',
    'fare_amount',
    'extra',
    'mta_tax',
    'tip_amount',
    'tolls_amount',
    'improvement_surcharge',
    'total_amount',
    'congestion_surcharge'
]
```

## Run the scaffolder

The suites generated here are **not meant to be production suites** - they are **scaffolds to build upon**.

**To get to a production grade suite, you will definitely want to [edit this
suite](https://docs.greatexpectations.io/en/latest/guides/how_to_guides/creating_and_editing_expectations/how_to_edit_an_expectation_suite_using_a_disposable_notebook.html?utm_source=notebook&utm_medium=scaffold_expectations)
after scaffolding gets you close to what you want.**

This is highly configurable depending on your goals.
You can ignore columns or exclude certain expectations, specify a threshold for creating value set expectations, or even specify semantic types for a given column.
You can find more information about [how to configure this profiler, including a list of the expectations that it uses, here.](https://docs.greatexpectations.io/en/latest/guides/how_to_guides/creating_and_editing_expectations/how_to_create_an_expectation_suite_with_the_user_configurable_profiler.html)




```python
profiler = UserConfigurableProfiler(profile_dataset=batch,
    ignored_columns=ignored_columns,
    excluded_expectations=None,
    not_null_only=False,
    primary_or_compound_key=False,
    semantic_types_dict=None,
    table_expectations_only=False,
    value_set_threshold="MANY",
    )

suite = profiler.build_suite()
```


    Profiling:   0%|          | 0/1 [00:00<?, ?it/s, Column=passenger_count]


    Creating an expectation suite with the following expectations:
    
    Table-Level Expectations
    expect_table_columns_to_match_ordered_list
    expect_table_row_count_to_be_between
    
    Expectations by Column
    Column Name: passenger_count | Column Data Type: INT | Cardinality: VERY_FEW
    expect_column_max_to_be_between
    expect_column_mean_to_be_between
    expect_column_median_to_be_between
    expect_column_min_to_be_between
    expect_column_proportion_of_unique_values_to_be_between
    expect_column_quantile_values_to_be_between
    expect_column_values_to_be_in_set
    expect_column_values_to_be_in_type_list
    expect_column_values_to_not_be_null
    
    
    

## Save & review the scaffolded Expectation Suite

Let's save the scaffolded expectation suite as a JSON file in the
`great_expectations/expectations` directory of your project and rebuild the Data
 Docs site to make it easy to review the scaffolded suite.


```python
context.save_expectation_suite(suite, expectation_suite_name)

results = LegacyCheckpoint(
    name="_temp_checkpoint",
    data_context=context,
    batches=[
        {
          "batch_kwargs": batch_kwargs,
          "expectation_suite_names": [expectation_suite_name]
        }
    ]
).run()
validation_result_identifier = results.list_validation_result_identifiers()[0]
context.build_data_docs()
context.open_data_docs(validation_result_identifier)
```

    c:\python\python39\lib\site-packages\jinja2\environment.py:1088: DeprecationWarning: 'soft_unicode' has been renamed to 'soft_str'. The old name will be removed in MarkupSafe 2.1.
      return concat(self.root_render_func(self.new_context(vars)))
    

## Next steps
After you review this scaffolded Expectation Suite in Data Docs you
should edit this suite to make finer grained adjustments to the expectations.
This can be done by running `great_expectations suite edit taxi.demo`.


```python
batch_kwargs_2 = {'path': 'D:\\gathi\\great_expecatation\\ge_tutorials\\great_expectations\\..\\data\\yellow_tripdata_sample_2019-02.csv', 'datasource': 'data__dir', 'data_asset_name': 'yellow_tripdata_sample_2019-02'}

my_checkpoint = LegacyCheckpoint(
    name="my_checkpoint",
    data_context=context,
    batches=[
        {
          "batch_kwargs": batch_kwargs_2,
          "expectation_suite_names": ["taxi.demo"]
        }
    ]
)

results = my_checkpoint.run()
```

    c:\python\python39\lib\site-packages\jinja2\environment.py:1088: DeprecationWarning: 'soft_unicode' has been renamed to 'soft_str'. The old name will be removed in MarkupSafe 2.1.
      return concat(self.root_render_func(self.new_context(vars)))
    


```python
# Save the Checkpoint to your Data Context
my_checkpoint_json = my_checkpoint.config.to_json_dict()
context.add_checkpoint(**my_checkpoint_json)
```




    <great_expectations.checkpoint.checkpoint.LegacyCheckpoint at 0x1b4b6d56760>




```python
# And here's how you can load it from your Data Context again
my_loaded_checkpoint = context.get_checkpoint("my_checkpoint")

# And then run validation again if you'd like
my_loaded_checkpoint.run()
```

    c:\python\python39\lib\site-packages\jinja2\environment.py:1088: DeprecationWarning: 'soft_unicode' has been renamed to 'soft_str'. The old name will be removed in MarkupSafe 2.1.
      return concat(self.root_render_func(self.new_context(vars)))
    




    {
      "run_results": {
        "ValidationResultIdentifier::taxi/demo/20210804T182440.545826Z/20210804T182440.545826Z/8479f9ad9e8961a6bac205d71c08618c": {
          "validation_result": {
            "evaluation_parameters": {},
            "results": [
              {
                "exception_info": {
                  "raised_exception": false,
                  "exception_message": null,
                  "exception_traceback": null
                },
                "success": true,
                "expectation_config": {
                  "kwargs": {
                    "column_list": [
                      "vendor_id",
                      "pickup_datetime",
                      "dropoff_datetime",
                      "passenger_count",
                      "trip_distance",
                      "rate_code_id",
                      "store_and_fwd_flag",
                      "pickup_location_id",
                      "dropoff_location_id",
                      "payment_type",
                      "fare_amount",
                      "extra",
                      "mta_tax",
                      "tip_amount",
                      "tolls_amount",
                      "improvement_surcharge",
                      "total_amount",
                      "congestion_surcharge"
                    ],
                    "result_format": {
                      "result_format": "SUMMARY",
                      "partial_unexpected_count": 20
                    }
                  },
                  "meta": {},
                  "expectation_type": "expect_table_columns_to_match_ordered_list"
                },
                "result": {
                  "observed_value": [
                    "vendor_id",
                    "pickup_datetime",
                    "dropoff_datetime",
                    "passenger_count",
                    "trip_distance",
                    "rate_code_id",
                    "store_and_fwd_flag",
                    "pickup_location_id",
                    "dropoff_location_id",
                    "payment_type",
                    "fare_amount",
                    "extra",
                    "mta_tax",
                    "tip_amount",
                    "tolls_amount",
                    "improvement_surcharge",
                    "total_amount",
                    "congestion_surcharge"
                  ]
                },
                "meta": {}
              },
              {
                "exception_info": {
                  "raised_exception": false,
                  "exception_message": null,
                  "exception_traceback": null
                },
                "success": true,
                "expectation_config": {
                  "kwargs": {
                    "max_value": 10000,
                    "min_value": 10000,
                    "result_format": {
                      "result_format": "SUMMARY",
                      "partial_unexpected_count": 20
                    }
                  },
                  "meta": {},
                  "expectation_type": "expect_table_row_count_to_be_between"
                },
                "result": {
                  "observed_value": 10000
                },
                "meta": {}
              },
              {
                "exception_info": {
                  "raised_exception": false,
                  "exception_message": null,
                  "exception_traceback": null
                },
                "success": false,
                "expectation_config": {
                  "kwargs": {
                    "column": "passenger_count",
                    "max_value": 1,
                    "min_value": 1,
                    "result_format": {
                      "result_format": "SUMMARY",
                      "partial_unexpected_count": 20
                    }
                  },
                  "meta": {},
                  "expectation_type": "expect_column_min_to_be_between"
                },
                "result": {
                  "observed_value": 0,
                  "element_count": 10000,
                  "missing_count": null,
                  "missing_percent": null
                },
                "meta": {}
              },
              {
                "exception_info": {
                  "raised_exception": false,
                  "exception_message": null,
                  "exception_traceback": null
                },
                "success": true,
                "expectation_config": {
                  "kwargs": {
                    "column": "passenger_count",
                    "max_value": 6,
                    "min_value": 6,
                    "result_format": {
                      "result_format": "SUMMARY",
                      "partial_unexpected_count": 20
                    }
                  },
                  "meta": {},
                  "expectation_type": "expect_column_max_to_be_between"
                },
                "result": {
                  "observed_value": 6,
                  "element_count": 10000,
                  "missing_count": null,
                  "missing_percent": null
                },
                "meta": {}
              },
              {
                "exception_info": {
                  "raised_exception": false,
                  "exception_message": null,
                  "exception_traceback": null
                },
                "success": false,
                "expectation_config": {
                  "kwargs": {
                    "column": "passenger_count",
                    "max_value": 1.5716,
                    "min_value": 1.5716,
                    "result_format": {
                      "result_format": "SUMMARY",
                      "partial_unexpected_count": 20
                    }
                  },
                  "meta": {},
                  "expectation_type": "expect_column_mean_to_be_between"
                },
                "result": {
                  "observed_value": 1.3577,
                  "element_count": 10000,
                  "missing_count": null,
                  "missing_percent": null
                },
                "meta": {}
              },
              {
                "exception_info": {
                  "raised_exception": false,
                  "exception_message": null,
                  "exception_traceback": null
                },
                "success": true,
                "expectation_config": {
                  "kwargs": {
                    "column": "passenger_count",
                    "max_value": 1.0,
                    "min_value": 1.0,
                    "result_format": {
                      "result_format": "SUMMARY",
                      "partial_unexpected_count": 20
                    }
                  },
                  "meta": {},
                  "expectation_type": "expect_column_median_to_be_between"
                },
                "result": {
                  "observed_value": 1.0,
                  "element_count": 10000,
                  "missing_count": null,
                  "missing_percent": null
                },
                "meta": {}
              },
              {
                "exception_info": {
                  "raised_exception": false,
                  "exception_message": null,
                  "exception_traceback": null
                },
                "success": false,
                "expectation_config": {
                  "kwargs": {
                    "allow_relative_error": "lower",
                    "column": "passenger_count",
                    "quantile_ranges": {
                      "quantiles": [
                        0.05,
                        0.25,
                        0.5,
                        0.75,
                        0.95
                      ],
                      "value_ranges": [
                        [
                          1,
                          1
                        ],
                        [
                          1,
                          1
                        ],
                        [
                          1,
                          1
                        ],
                        [
                          2,
                          2
                        ],
                        [
                          5,
                          5
                        ]
                      ]
                    },
                    "result_format": {
                      "result_format": "SUMMARY",
                      "partial_unexpected_count": 20
                    }
                  },
                  "meta": {},
                  "expectation_type": "expect_column_quantile_values_to_be_between"
                },
                "result": {
                  "observed_value": {
                    "quantiles": [
                      0.05,
                      0.25,
                      0.5,
                      0.75,
                      0.95
                    ],
                    "values": [
                      0,
                      1,
                      1,
                      1,
                      5
                    ]
                  },
                  "element_count": 10000,
                  "missing_count": null,
                  "missing_percent": null,
                  "details": {
                    "success_details": [
                      false,
                      true,
                      true,
                      false,
                      true
                    ]
                  }
                },
                "meta": {}
              },
              {
                "exception_info": {
                  "raised_exception": false,
                  "exception_message": null,
                  "exception_traceback": null
                },
                "success": false,
                "expectation_config": {
                  "kwargs": {
                    "column": "passenger_count",
                    "value_set": [
                      1,
                      2,
                      3,
                      4,
                      5,
                      6
                    ],
                    "result_format": {
                      "result_format": "SUMMARY",
                      "partial_unexpected_count": 20
                    }
                  },
                  "meta": {},
                  "expectation_type": "expect_column_values_to_be_in_set"
                },
                "result": {
                  "element_count": 10000,
                  "missing_count": 0,
                  "missing_percent": 0.0,
                  "unexpected_count": 1579,
                  "unexpected_percent": 15.790000000000001,
                  "unexpected_percent_total": 15.790000000000001,
                  "unexpected_percent_nonmissing": 15.790000000000001,
                  "partial_unexpected_list": [
                    0,
                    0,
                    0,
                    0,
                    0,
                    0,
                    0,
                    0,
                    0,
                    0,
                    0,
                    0,
                    0,
                    0,
                    0,
                    0,
                    0,
                    0,
                    0,
                    0
                  ],
                  "partial_unexpected_index_list": [
                    14,
                    24,
                    78,
                    101,
                    141,
                    160,
                    162,
                    173,
                    174,
                    175,
                    176,
                    177,
                    178,
                    179,
                    180,
                    181,
                    182,
                    183,
                    184,
                    185
                  ],
                  "partial_unexpected_counts": [
                    {
                      "value": 0,
                      "count": 1579
                    }
                  ]
                },
                "meta": {}
              },
              {
                "exception_info": {
                  "raised_exception": false,
                  "exception_message": null,
                  "exception_traceback": null
                },
                "success": true,
                "expectation_config": {
                  "kwargs": {
                    "column": "passenger_count",
                    "result_format": {
                      "result_format": "SUMMARY",
                      "partial_unexpected_count": 20
                    }
                  },
                  "meta": {},
                  "expectation_type": "expect_column_values_to_not_be_null"
                },
                "result": {
                  "element_count": 10000,
                  "unexpected_count": 0,
                  "unexpected_percent": 0.0,
                  "unexpected_percent_total": 0.0,
                  "partial_unexpected_list": []
                },
                "meta": {}
              },
              {
                "exception_info": {
                  "raised_exception": false,
                  "exception_message": null,
                  "exception_traceback": null
                },
                "success": false,
                "expectation_config": {
                  "kwargs": {
                    "column": "passenger_count",
                    "max_value": 0.0006,
                    "min_value": 0.0006,
                    "result_format": {
                      "result_format": "SUMMARY",
                      "partial_unexpected_count": 20
                    }
                  },
                  "meta": {},
                  "expectation_type": "expect_column_proportion_of_unique_values_to_be_between"
                },
                "result": {
                  "observed_value": 0.0007,
                  "element_count": 10000,
                  "missing_count": null,
                  "missing_percent": null
                },
                "meta": {}
              },
              {
                "exception_info": {
                  "raised_exception": false,
                  "exception_message": null,
                  "exception_traceback": null
                },
                "success": true,
                "expectation_config": {
                  "kwargs": {
                    "column": "passenger_count",
                    "type_list": [
                      "INTEGER",
                      "integer",
                      "int",
                      "int_",
                      "int8",
                      "int16",
                      "int32",
                      "int64",
                      "uint8",
                      "uint16",
                      "uint32",
                      "uint64",
                      "INT",
                      "INTEGER",
                      "INT64",
                      "TINYINT",
                      "BYTEINT",
                      "SMALLINT",
                      "BIGINT",
                      "IntegerType",
                      "LongType"
                    ],
                    "result_format": {
                      "result_format": "SUMMARY",
                      "partial_unexpected_count": 20
                    }
                  },
                  "meta": {},
                  "expectation_type": "expect_column_values_to_be_in_type_list"
                },
                "result": {
                  "observed_value": "int64"
                },
                "meta": {}
              }
            ],
            "success": false,
            "statistics": {
              "evaluated_expectations": 11,
              "successful_expectations": 6,
              "unsuccessful_expectations": 5,
              "success_percent": 54.54545454545454
            },
            "meta": {
              "great_expectations_version": "0.13.25",
              "expectation_suite_name": "taxi.demo",
              "run_id": {
                "run_name": "20210804T182440.545826Z",
                "run_time": "2021-08-04T18:24:40.545826+00:00"
              },
              "batch_kwargs": {
                "path": "D:\\gathi\\great_expecatation\\ge_tutorials\\great_expectations\\..\\data\\yellow_tripdata_sample_2019-02.csv",
                "datasource": "data__dir",
                "data_asset_name": "yellow_tripdata_sample_2019-02"
              },
              "batch_markers": {
                "ge_load_time": "20210804T182440.507853Z",
                "pandas_data_fingerprint": "88b447d903f05fb594b87b13de399e45"
              },
              "batch_parameters": null,
              "validation_time": "20210804T182440.551826Z",
              "expectation_suite_meta": {
                "columns": {
                  "congestion_surcharge": {
                    "description": ""
                  },
                  "dropoff_datetime": {
                    "description": ""
                  },
                  "dropoff_location_id": {
                    "description": ""
                  },
                  "extra": {
                    "description": ""
                  },
                  "fare_amount": {
                    "description": ""
                  },
                  "improvement_surcharge": {
                    "description": ""
                  },
                  "mta_tax": {
                    "description": ""
                  },
                  "passenger_count": {
                    "description": ""
                  },
                  "payment_type": {
                    "description": ""
                  },
                  "pickup_datetime": {
                    "description": ""
                  },
                  "pickup_location_id": {
                    "description": ""
                  },
                  "rate_code_id": {
                    "description": ""
                  },
                  "store_and_fwd_flag": {
                    "description": ""
                  },
                  "tip_amount": {
                    "description": ""
                  },
                  "tolls_amount": {
                    "description": ""
                  },
                  "total_amount": {
                    "description": ""
                  },
                  "trip_distance": {
                    "description": ""
                  },
                  "vendor_id": {
                    "description": ""
                  }
                },
                "great_expectations_version": "0.13.25"
              }
            }
          },
          "actions_results": {
            "store_validation_result": {
              "class": "StoreValidationResultAction"
            },
            "store_evaluation_params": {
              "class": "StoreEvaluationParametersAction"
            },
            "update_data_docs": {
              "local_site": "file://D:\\gathi\\great_expecatation\\ge_tutorials\\great_expectations\\uncommitted/data_docs/local_site/validations\\taxi\\demo\\20210804T182440.545826Z\\20210804T182440.545826Z\\8479f9ad9e8961a6bac205d71c08618c.html",
              "class": "UpdateDataDocsAction"
            }
          }
        }
      },
      "evaluation_parameters": null,
      "success": false,
      "run_id": {
        "run_name": "20210804T182440.545826Z",
        "run_time": "2021-08-04T18:24:40.545826+00:00"
      },
      "validation_operator_config": {
        "class_name": "ActionListValidationOperator",
        "module_name": "great_expectations.validation_operators",
        "name": "default-action-list-validation-operator",
        "kwargs": {
          "action_list": [
            {
              "name": "store_validation_result",
              "action": {
                "class_name": "StoreValidationResultAction"
              }
            },
            {
              "name": "store_evaluation_params",
              "action": {
                "class_name": "StoreEvaluationParametersAction"
              }
            },
            {
              "name": "update_data_docs",
              "action": {
                "class_name": "UpdateDataDocsAction",
                "site_names": []
              }
            }
          ],
          "result_format": {
            "result_format": "SUMMARY",
            "partial_unexpected_count": 20
          }
        }
      }
    }




```python
validation_result_identifier = results.list_validation_result_identifiers()[0]
context.build_data_docs()
context.open_data_docs(validation_result_identifier)
```


```python

```
