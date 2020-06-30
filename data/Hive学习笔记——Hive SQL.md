# Hive学习笔记——Hive SQL

### 数据类型

#### 基本数据类型

|    类型     |                      描述                      | 示例 |
| :---------: | :--------------------------------------------: | :--: |
|   boolean   |                   true/false                   |      |
|   tinyint   |             1-byte signed integer              |      |
|  smallint   |             2-byted signed integer             |      |
| int/integer |             4-byte signed integer              |      |
|   bigint    |             8-byte signed integer              |      |
|    float    | 4-byte single precision floating point number  |      |
|   double    | 8-point double precision floating point number |      |
|   decimal   |         with a precision of 38 digits          |      |
|   numeric   |                same as decimal                 |      |
|  timestamp  |      supports traditional UNIX timestamp       |      |
|    date     |            int the form YYYY-MM-DD             |      |
|  interval   |                                                |      |
|   string    |                                                |      |
|   varchar   |                                                |      |
|    char     |                                                |      |
|   binary    |                                                |      |

### 复杂类型

| 类型    | 描述                                                  | 示例 |
| ------- | ----------------------------------------------------- | ---- |
| arrays  | ARRAY<data_type>                                      |      |
| maps    | MAP<primitive_type, data_type>                        |      |
| structs | STRUCT<col_name: data_type[COMMENT col_comment], ...> |      |
| union   | UNIONTYPE<data_type, data_type, ...?                  |      |

