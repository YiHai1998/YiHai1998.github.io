# mysql到hdfs

## 在hdfs上创建文件夹

```python

```

## 手动

```
hadoop fs -mkdir -p /origin_data/gmall/db/activity_info
hadoop fs -mkdir -p /origin_data/gmall/db/activity_order
hadoop fs -mkdir -p /origin_data/gmall/db/activity_rule
hadoop fs -mkdir -p /origin_data/gmall/db/activity_sku
hadoop fs -mkdir -p /origin_data/gmall/db/base_category1
hadoop fs -mkdir -p /origin_data/gmall/db/base_category2
hadoop fs -mkdir -p /origin_data/gmall/db/base_category3
hadoop fs -mkdir -p /origin_data/gmall/db/base_dic
hadoop fs -mkdir -p /origin_data/gmall/db/base_province
hadoop fs -mkdir -p /origin_data/gmall/db/base_region
hadoop fs -mkdir -p /origin_data/gmall/db/base_trademark
hadoop fs -mkdir -p /origin_data/gmall/db/cart_info
hadoop fs -mkdir -p /origin_data/gmall/db/comment_info
hadoop fs -mkdir -p /origin_data/gmall/db/coupon_info
hadoop fs -mkdir -p /origin_data/gmall/db/coupon_use
hadoop fs -mkdir -p /origin_data/gmall/db/date_info
hadoop fs -mkdir -p /origin_data/gmall/db/favor_info
hadoop fs -mkdir -p /origin_data/gmall/db/holiday_info
hadoop fs -mkdir -p /origin_data/gmall/db/holiday_year
hadoop fs -mkdir -p /origin_data/gmall/db/order_detail
hadoop fs -mkdir -p /origin_data/gmall/db/order_info
hadoop fs -mkdir -p /origin_data/gmall/db/order_refund_info
hadoop fs -mkdir -p /origin_data/gmall/db/order_status_log
hadoop fs -mkdir -p /origin_data/gmall/db/payment_info
hadoop fs -mkdir -p /origin_data/gmall/db/sku_info
hadoop fs -mkdir -p /origin_data/gmall/db/spu_info
hadoop fs -mkdir -p /origin_data/gmall/db/user_info
```

## json

### activity_info

mysql2hdfs_activity_info.json

```
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "activity_name",
                            "activity_type",
                            "activity_desc",
                            "start_time",
                            "end_time",
                            "create_time"
                        ],
                        "connection": [
                            {
                                "table": [
                                    "activity_info"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/activity_info",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "activity_name",
                                "type": "string"
                            },
                            {
                                "name": "activity_type",
                                "type": "string"
                            },
                            {
                                "name": "activity_desc",
                                "type": "string"
                            },
                            {
                                "name": "start_time",
                                "type": "string"
                            },
                            {
                                "name": "end_time",
                                "type": "string"
                            },
                                                        {
                                "name": "create_time",
                                "type": "string"
                            },
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```

### activity_order

mysql2hdfs_activity_order.json

```
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "activity_id",
                            "order_id",
                            "create_time"
                        ],
                        "connection": [
                            {
                                "table": [
                                    "activity_order"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/activity_order",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "activity_id",
                                "type": "int"
                            },
                            {
                                "name": "order_id",
                                "type": "int"
                            },
                            {
                                "name": "create_time",
                                "type": "timestamp"
                            }
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
python2 ../bin/datax.py mysql2hdfs_activity_order.json
```

### activity_rule

mysql2hdfs_activity_rule.json

```
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "activity_id",
                            "condition_amount",
                            "condition_num",
                            "benefit_amount",
                            "benefit_discount",
                            "benefit_level",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "activity_rule"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/activity_rule",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "activity_id",
                                "type": "int"
                            },
                            {
                                "name": "condition_amount",
                                "type": "double"
                            },
                            {
                                "name": "condition_num",
                                "type": "int"
                            },
                            {
                                "name": "benefit_amount",
                                "type": "double"
                            },
                            {
                                "name": "benefit_discount",
                                "type": "int"
                            },
                            {
                                "name": "benefit_level",
                                "type": "int"
                            },
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```

### activity_sku

mysql2hdfs_activity_sku.json

```
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "activity_id",
                            "sku_id",
                            "create_time",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "activity_sku"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/activity_sku",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "activity_id",
                                "type": "int"
                            },
                            {
                                "name": "sku_id",
                                "type": "int"
                            },
                            {
                                "name": "create_time",
                                "type": "timestamp"
                            },
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
python2 ../bin/datax.py mysql2hdfs_activity_sku.json
```

### base_category1

mysql2hdfs_base_category1.json

```
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "name",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "base_category1"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/base_category1",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "name",
                                "type": "string"
                            },
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```

### base_category2

mysql2hdfs_base_category2.json

```
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "name",
                            "category1_id",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "base_category2"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/base_category2",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "name",
                                "type": "string"
                            },
                            {
                                "name": "category1_id",
                                "type": "int"
                            },
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```

### base_category3

mysql2hdfs_base_category3.json

```
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "name",
                            "category2_id",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "base_category3"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/base_category3",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "name",
                                "type": "string"
                            },
                            {
                                "name": "category2_id",
                                "type": "int"
                            },
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```



### base_dic

mysql2hdfs_base_dic.json

```
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "dic_code",
                            "dic_name",
                            "parent_code",
                            "create_time",
                            "operate_time",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "base_dic"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/base_dic",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "dic_code",
                                "type": "string"
                            },
                            {
                                "name": "dic_name",
                                "type": "string"
                            },
                            {
                                "name": "parent_code",
                                "type": "string"
                            },
                            {
                                "name": "create_time",
                                "type": "timestamp"
                            },
                             {
                                "name": "operate_time",
                                "type": "timestamp"
                            },         
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```



### base_province

mysql2hdfs_base_province.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "name",
                            "region_id",
                            "area_code",
                            "iso_code",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "base_province"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/base_province",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "name",
                                "type": "string"
                            },
                            {
                                "name": "region_id",
                                "type": "string"
                            },
                            {
                                "name": "area_code",
                                "type": "string"
                            },
                             {
                                "name": "iso_code",
                                "type": "varchar"
                            },         
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```

### base_region

mysql2hdfs_base_region.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "region_name",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "base_region"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/base_region",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "string"
                            },
                            {
                                "name": "dic_name",
                                "type": "string"
                            },
   
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```

### base_trademark

mysql2hdfs_base_trademark.json

```
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "tm_id",
                            "tm_name",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "base_trademark"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/base_trademark",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "tm_id",
                                "type": "string"
                            },
                            {
                                "name": "tm_name",
                                "type": "string"
                            },
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```



### cart_info

mysql2hdfs_cart_info.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "user_id",
                            "sku_id",
                            "cart_price",
                            "sku_num",
                            "img_url",
                            "sku_name",
                            "create_time",
                            "operate_time",
                            "is_ordered",
                            "order_time",
                            "source_type",
                            "source_id",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "cart_info"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/cart_info",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "user_id",
                                "type": "int"
                            },
                            {
                                "name": "sku_id",
                                "type": "int"
                            },
                            {
                                "name": "cart_price",
                                "type": "double"
                            },
                             {
                                "name": "sku_num",
                                "type": "int"
                            },  
                            {
                                "name": "img_url",
                                "type": "string"
                            },
                            {
                                "name": "sku_name",
                                "type": "string"
                            },
                            {
                                "name": "create_time",
                                "type": "timestamp"
                            },
                             {
                                "name": "operate_time",
                                "type": "timestamp"
                            },   
                            {
                                "name": "is_ordered",
                                "type": "int"
                            },
                            {
                                "name": "order_time",
                                "type": "timestamp"
                            },
                            {
                                "name": "source_type",
                                "type": "string"
                            },
                            {
                                "name": "source_id",
                                "type": "int"
                            },
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```



### comment_info

mysql2hdfs_comment_info.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "user_id",
                            "sku_id",
                            "spu_id",
                            "order_id",
                            "appraise",
                            "comment_txt",
                            "create_time",
                            "operate_time",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "comment_info"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/comment_info",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "string"
                            },
                            {
                                "name": "user_id",
                                "type": "int"
                            },
                            {
                                "name": "sku_id",
                                "type": "int"
                            },
                            {
                                "name": "spu_id",
                                "type": "int"
                            },
                             {
                                "name": "order_id",
                                "type": "int"
                            },
                            {
                                "name": "appraise",
                                "type": "string"
                            },
                             {
                                "name": "comment_txt",
                                "type": "string"
                            }, 
                            {
                                "name": "create_time",
                                "type": "timestamp"
                            },
                             {
                                "name": "operate_time",
                                "type": "timestamp"
                            },      
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```

### coupon_info

mysql2hdfs_coupon_info.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "coupon_name",
                            "coupon_type",
                            "condition_amount",
                            "condition_num",
                            "activity_id",
                            "benefit_amount",
                            "benefit_discount",
                            "create_time",
                            "range_type",
                            "spu_id",
                            "tm_id",
                            "category3_id",
                            "limit_num",
                            "operate_time",
                            "expire_time",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "coupon_info"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/coupon_info",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "coupon_name",
                                "type": "string"
                            },
                            {
                                "name": "coupon_type",
                                "type": "string"
                            },
                            {
                                "name": "condition_amount",
                                "type": "double"
                            },
                             {
                                "name": "condition_num",
                                "type": "timestamp"
                            },
                            {
                                "name": "activity_id",
                                "type": "int"
                            },
                            {
                                "name": "benefit_amount",
                                "type": "double"
                            },
                            {
                                "name": "benefit_discount",
                                "type": "int"
                            },
                            {
                                "name": "create_time",
                                "type": "timestamp"
                            },
                             {
                                "name": "range_type",
                                "type": "string"
                            },  
                            {
                                "name": "spu_id",
                                "type": "int"
                            },
                            {
                                "name": "tm_id",
                                "type": "int"
                            },
                            {
                                "name": "category3_id",
                                "type": "int"
                            },
                            {
                                "name": "limit_num",
                                "type": "int"
                            },
                             {
                                "name": "operate_time",
                                "type": "timestamp"
                            },
                             {
                                "name": "expire_time",
                                "type": "timestamp"
                            },  
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```



### coupon_use

mysql2hdfs_coupon_use.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "coupon_id",
                            "user_id",
                            "order_id",
                            "coupon_status",
                            "get_time",
                            "using_time",
                            "used_time",
                            "expire_time",                           
                        ],
                        "connection": [
                            {
                                "table": [
                                    "coupon_use"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/coupon_use",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "coupon_id",
                                "type": "int"
                            },
                            {
                                "name": "user_id",
                                "type": "int"
                            },
                            {
                                "name": "order_id",
                                "type": "int"
                            },
                             {
                                "name": "coupon_status",
                                "type": "string"
                            }, 
                            {
                                "name": "get_time",
                                "type": "timestamp"
                            },
                            {
                                "name": "using_time",
                                "type": "timestamp"
                            },
                            {
                                "name": "used_time",
                                "type": "timestamp"
                            },
                             {
                                "name": "expire_time",
                                "type": "timestamp"
                            },       
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```



### date_info

mysql2hdfs_date_info.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "date_id",
                            "week_id",
                            "week_day",
                            "day",
                            "month",
                            "quarter",
                            "year",
                            "is_workday",
                            "holiday_id",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "date_info"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/date_info",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "date_id",
                                "type": "int"
                            },
                            {
                                "name": "week_id",
                                "type": "int"
                            },
                            {
                                "name": "week_day",
                                "type": "int"
                            },
                            {
                                "name": "day",
                                "type": "int"
                            },
                             {
                                "name": "month",
                                "type": "int"
                            }, 
                            {
                                "name": "quarter",
                                "type": "int"
                            },
                            {
                                "name": "year",
                                "type": "int"
                            },
                            {
                                "name": "is_workday",
                                "type": "int"
                            },
                             {
                                "name": "holiday_id",
                                "type": "int"
                            },      
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```



### favor_info

mysql2hdfs_favor_info.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "user_id",
                            "sku_id",
                            "spu_id",
                            "is_cancel",
                             "create_time",
                            "cancel_time",                           
                        ],
                        "connection": [
                            {
                                "table": [
                                    "favor_info"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/favor_info",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "string"
                            },
                            {
                                "name": "user_id",
                                "type": "int"
                            },
                            {
                                "name": "sku_id",
                                "type": "int"
                            },
                            {
                                "name": "spu_id",
                                "type": "int"
                            },
                             {
                                "name": "is_cancel",
                                "type": "string"
                            },
                            {
                                "name": "create_time",
                                "type": "timestamp"
                            },
                             {
                                "name": "cancel_time",
                                "type": "timestamp"
                            },      
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```

### holiday_info

mysql2hdfs_holiday_info.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "holiday_id",
                            "holiday_name",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "holiday_info"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/holiday_info",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "holiday_id",
                                "type": "int"
                            },
                            {
                                "name": "holiday_name",
                                "type": "string"
                            },   
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```



### holiday_year

mysql2hdfs_holiday_year.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "year_id",
                            "holiday_id",
                            "start_date_id",
                            "end_date_id",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "holiday_year"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/holiday_year",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "year_id",
                                "type": "int"
                            },
                            {
                                "name": "holiday_id",
                                "type": "int"
                            },
                            {
                                "name": "start_date_id",
                                "type": "int"
                            },
                            {
                                "name": "end_date_id",
                                "type": "int"
                            }     
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```

### order_detail

mysql2hdfs_order_detail.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "order_id",
                            "sku_id",
                            "sku_name",
                            "img_url",
                            "order_price",
                            "sku_num",
                            "create_time",
                            "source_type",
                            "source_id",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "order_detail"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/order_detail",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "order_id",
                                "type": "int"
                            },
                            {
                                "name": "sku_id",
                                "type": "int"
                            },
                            {
                                "name": "sku_name",
                                "type": "string"
                            },
                             {
                                "name": "img_url",
                                "type": "string"
                            },
                            {
                                "name": "order_price",
                                "type": "double"
                            },
                            {
                                "name": "sku_num",
                                "type": "string"
                            },
                            {
                                "name": "create_time",
                                "type": "timestamp"
                            },
                            {
                                "name": "source_type",
                                "type": "string"
                            },
                             {
                                "name": "source_id",
                                "type": "id"
                            },        
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```

### order_info

mysql2hdfs_order_info.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "consignee",
                            "consignee_tel",
                            "final_total_amount",
                            "order_status",
                            "user_id",
                            "delivery_address",
                            "order_comment",
                            "out_trade_no",
                            "trade_body",
                            "create_time",
                            "operate_time",
                            "expire_time",
                            "tracking_no",
                            "parent_order_id",
                            "img_url",
                            "province_id",
                            "benefit_reduce_amount",
                            "original_total_amount",
                            "feight_fee",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "order_info"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/order_info",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "consignee",
                                "type": "string"
                            },
                            {
                                "name": "consignee_tel",
                                "type": "string"
                            },
                            {
                                "name": "final_total_amount",
                                "type": "double"
                            },
                             {
                                "name": "order_status",
                                "type": "string"
                            }, 
                            {
                                "name": "user_id",
                                "type": "int"
                            },
                            {
                                "name": "delivery_address",
                                "type": "string"
                            },
                            {
                                "name": "order_comment",
                                "type": "string"
                            },
                            {
                                "name": "out_trade_no",
                                "type": "string"
                            },
                             {
                                "name": "trade_body",
                                "type": "string"
                            },  
                            {
                                "name": "create_time",
                                "type": "timestamp"
                            },
                            {
                                "name": "operate_time",
                                "type": "timestamp"
                            },
                            {
                                "name": "expire_time",
                                "type": "timestamp"
                            },
                            {
                                "name": "tracking_no",
                                "type": "string"
                            },
                            {
                                "name": "parent_order_id",
                                "type": "int"
                            },
                             {
                                "name": "img_url",
                                "type": "string"
                            }, 
                            {
                                "name": "province_id",
                                "type": "int"
                            },
                            {
                                "name": "benefit_reduce_amount",
                                "type": "double"
                            },
                            {
                                "name": "original_total_amount",
                                "type": "double"
                            },
                            {
                                "name": "feight_fee",
                                "type": "double"
                            }
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```

### order_refund_info

mysql2hdfs_order_refund_info.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "user_id",
                            "order_id",
                            "sku_id",
                            "refund_type",
                            "refund_num",
                            "refund_amount",
                            "refund_reason_type",
                            "refund_reason_txt",
                            "create_time",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "order_refund_info"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/order_refund_info",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "user_id",
                                "type": "int"
                            },
                            {
                                "name": "order_id",
                                "type": "int"
                            },
                            {
                                "name": "sku_id",
                                "type": "int"
                            },
                             {
                                "name": "refund_type",
                                "type": "string"
                            },
							{
                                "name": "refund_num",
                                "type": "int"
                            },
                            {
                                "name": "refund_amount",
                                "type": "double"
                            },
                            {
                                "name": "refund_reason_type",
                                "type": "string"
                            },
                            {
                                "name": "refund_reason_txt",
                                "type": "string"
                            },
                             {
                                "name": "create_time",
                                "type": "timestamp"
                            },            
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```

### order_status_log

mysql2hdfs_order_status_log.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "order_id",
                            "order_status",
                            "operate_time",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "order_status_log"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/order_status_log",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "order_id",
                                "type": "int"
                            },
                            {
                                "name": "order_status",
                                "type": "string"
                            },
                             {
                                "name": "operate_time",
                                "type": "timestamp"
                            },         
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```

### payment_info

mysql2hdfs_payment_info.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "out_trade_no",
                            "order_id",
                            "user_id",
                            "alipay_trade_no",
                            "total_amount",
                            "subject",
                            "payment_type",
                            "payment_time",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "payment_info"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/payment_info",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "out_trade_no",
                                "type": "string"
                            },
                            {
                                "name": "order_id",
                                "type": "int"
                            },
                            {
                                "name": "user_id",
                                "type": "int"
                            },
                             {
                                "name": "alipay_trade_no",
                                "type": "string"
                            },
                            {
                                "name": "total_amount",
                                "type": "double"
                            },
                            {
                                "name": "subject",
                                "type": "string"
                            },
                             {
                                "name": "payment_type",
                                "type": "string"
                            }, 
                            {
                                "name": "payment_time",
                                "type": "timestamp"
                            }, 
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```

### sku_info

mysql2hdfs_sku_info.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "spu_id",
                            "price",
                            "sku_name",
                            "sku_desc",
                            "weight",
                            "tm_id",
                            "category3_id",
                            "sku_default_img",
                            "create_time",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "sku_info"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/sku_info",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "spu_id",
                                "type": "int"
                            },
                            {
                                "name": "price",
                                "type": "double"
                            },
                            {
                                "name": "sku_name",
                                "type": "string"
                            },
                             {
                                "name": "sku_desc",
                                "type": "string"
                            },   
                            {
                                "name": "weight",
                                "type": "string"
                            },
                            {
                                "name": "tm_id",
                                "type": "int"
                            },
                            {
                                "name": "category3_id",
                                "type": "string"
                            },
                            {
                                "name": "sku_default_img",
                                "type": "string"
                            },
                             {
                                "name": "create_time",
                                "type": "timestamp"
                            },   
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```

### spu_info

mysql2hdfs_spu_info.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "spu_name",
                            "description",
                            "category3_id",
                            "tm_id",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "spu_info"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/spu_info",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "spu_name",
                                "type": "string"
                            },
                            {
                                "name": "description",
                                "type": "string"
                            },
                            {
                                "name": "category3_id",
                                "type": "int"
                            },
                             {
                                "name": "tm_id",
                                "type": "int"
                            },         
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```

### user_info

mysql2hdfs_user_info.json

```json
{
    "job": {
        "setting": {
            "speed": {
                 "channel": 3
            },
            "errorLimit": {
                "record": 0,
                "percentage": 0.02
            }
        },
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "root",
                        "password": "root",
                        "column": [
                            "id",
                            "login_name",
                            "nick_name",
                            "passwd",
                            "name",
                            "phone_num",
                            "email",
                            "head_img",
                            "user_level",
                            "birthday",
                            "gender",
                            "create_time",
                            "operate_time",
                        ],
                        "connection": [
                            {
                                "table": [
                                    "user_info"
                                ],
                                "jdbcUrl": [
     "jdbc:mysql://127.0.0.1:3306/gmall"
                                ]
                            }
                        ]
                    }
                },
 "writer": {
                    "name": "hdfswriter",
                    "parameter": {
                        "defaultFS": "hdfs://tsingdata01:8020",
                        "fileType": "text",
                        "path": "/origin_data/gmall/db/user_info",
                        "fileName": "2021-05-26",
                        "column": [
                            {
                                "name": "id",
                                "type": "int"
                            },
                            {
                                "name": "login_name",
                                "type": "string"
                            },
                            {
                                "name": "nick_name",
                                "type": "string"
                            },
                            {
                                "name": "passwd",
                                "type": "string"
                            },
                             {
                                "name": "name",
                                "type": "string"
                            },
                            {
                                "name": "phone_num",
                                "type": "string"
                            },
                            {
                                "name": "email",
                                "type": "string"
                            },
                            {
                                "name": "head_img",
                                "type": "string"
                            },
                            {
                                "name": "user_level",
                                "type": "string"
                            },
                             {
                                "name": "birthday",
                                "type": "timestamp"
                            },  
                            {
                                "name": "gender",
                                "type": "string"
                            },
                            {
                                "name": "create_time",
                                "type": "timestamp"
                            },
                            {
                                "name": "operate_time",
                                "type": "timestamp"
                            }
                        ],
                        "writeMode": "append",
                        "fieldDelimiter": "\t",
                    }
                }
            }
        ]
    }
}
```



# 脚本

```python
import os

# job路径
filedir = "/opt/module/datax/job/gmall"

for root, dirs, files in os.walk(filedir):
    for name in files:
        os.system("python2 /opt/module/datax/bin/datax.py /opt/module/datax/job/gmall/" + str(name))
```



```
python mysql_to_hdfs.py
```

