# DBT 的自动模型生成

> 原文：<https://medium.com/codex/auto-model-generating-for-dbt-97a856506534?source=collection_archive---------5----------------------->

## 用于取消嵌套转移数据(红移)的简单 CLI

在本文中，我们将看到如何使用 Python 来自动化我们的 dbt 模型生成步骤。

我研究微服务和 Kubernetes 已经快 2 周了。因此，我不能颠倒新的东西来写🫠。实际上，我已经在我正在做的项目中实现了一些新的技术栈。为了让你对未来感到兴奋，我会说我们将看到如何实现 OpenSearch 我们的项目来创建我们自己的搜索服务。🥸

![](img/9417a76af9a202161ff00e57a47734a1.png)

照片由[卢卡斯](https://unsplash.com/@lukash?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 我的日常任务之一(有时😋)

我在一家咨询公司工作，我们为我们的一个客户开发一个 IT 基础设施项目。客户的数据团队需要更新暂存表和数据集市，以改进他们的报告。为了更新相关的表，我需要检查嵌套的数据结构(JSON 格式)并更新我的 dbt 模型中添加或删除的列。直到出现了一个几乎有 75 列的表格，我才觉得好受些。🤯那一次，我编写了一个简单的 Python cli，而不是花费时间逐行转换 75 列。我已经在我们的 VCS 上发布了回购，口号如下。

> 我创建了这个脚本，因为我懒得一行一行地写:&

# DBT 模型生成器

我们的文件夹树如下所示。

```
├── helpers
│   ├── connection.py
│   ├── __init__.py
│   ├── parser.py
│   └── writer.py
├── README.md
├── requirements.txt
└── script.py
```

您可以使用下面的代码安装所需的包。

```
pip install black SQLAlchemy sqlalchemy-redshift psycopg2-binary
```

## 连接模块

通过使用下面的函数，我们可以很容易地获得数据库连接。这个函数返回数据库连接。我们将使用`con`对象来执行 sql 命令。

```
# helpers/connection.py

from sqlalchemy import create_engine

def get_engine():
    """Returns Redshift engine

    Returns:
        engine: Redshift connected engine
    """
    return create_engine(
        "redshift+psycopg2://<YOUR_URI>" # ofcourse you can use env variables
    )
```

## 分析器模块

解析器模块帮助我们解析每一列及其类型(对于红移)。

```
# helpers/parser.py

import json
from datetime import datetime

class TypeChecker(object):
    @staticmethod
    def is_timestamp(val: str) -> bool:
        try:
            datetime.fromisoformat(val)
            return True
        except:
            return False

    @staticmethod
    def is_integer(val: str) -> bool:
        try:
            int(val)
            return True
        except:
            return False

    @staticmethod
    def is_decimal(val: str) -> bool:
        try:
            float(val)
            return True
        except:
            return False

    @staticmethod
    def is_varchar(val: str) -> bool:
        try:
            str(val)
            return True
        except:
            return False

def get_data_type(col_value: str) -> str:
    if TypeChecker.is_timestamp(col_value):
        return "timestamp"
    elif TypeChecker.is_integer(col_value):
        return "integer"
    elif TypeChecker.is_decimal(col_value):
        return "decimal(16,2)"
    elif (
        TypeChecker.is_varchar(col_value) == False
    ):  # if it's not varchar, it has to be examined
        return "UNDEFINED"
    else:
        return "varchar"

def get_parsed_data(res: str) -> dict:
    """Return passed data columns' types

    Args:
        res (str): JSON format data in STR!

    Returns:
        column_types (dict): columns are keys and data types are values
    """
    res = json.loads(res)

    column_types = {}

    for col in res:
        column_types[col] = get_data_type(res[col])

    return column_types
```

## 写入模块

这个模块帮助我们生成 dbt 模型结构(字符串),如下图所示。

`json_identifier."col_name"::col_type as col`

```
# helpers/writer.py

def get_dbt_formatted_sql(data: dict, json_col_id: str) -> str:
    rows = [
        f'{json_col_id}."{col}"::{data[col]} as {col}'
        for col in data
        if col != "_extract_timestamp"
    ]
    output = ",\n".join(rows)
    output += f',\n{json_col_id}."_extract_timestamp"::timestamp as _extract_timestamp'
    return output
```

## 主脚本

我们可以直接运行这个脚本来为嵌套的数据表生成 dbt 模型。脚本执行以下操作。

*   从表格中选择第一行
*   解析它的列名和数据类型
*   生成 dbt 模型输出
*   将输出写入文本文件

```
# ./script.py

import sys
from helpers.connection import get_engine
from helpers.parser import get_parsed_data
from helpers.writer import get_dbt_formatted_sql

SCHEMA_PREFIX = "my_aws_schema"
JSON_PREFIX = "json_identifier"

def main():
    global SCHEMA_PREFIX
    global JSON_PREFIX

    table = sys.argv[1]

    try:
        SCHEMA_PREFIX = sys.argv[2]  # if 2nd argument is passed, it is schema!
    except:
        print(f"pre-defined schema is => {SCHEMA_PREFIX}")

    try:
        SCHEMA_PREFIX = sys.argv[
            3
        ]  # if 3rd argument is passed, it is JSON column identifier!
    except:
        print(f"pre-defined JSON identifier is => {JSON_PREFIX}")

    engine = get_engine()

    res = engine.execute(f"select * from {SCHEMA_PREFIX}.{table} limit 1;").fetchone()[
        0
    ]

    column_types = get_parsed_data(res)

    with open("raw_data.json", "w+", encoding="utf-8") as file:
        file.write(str(res))

    with open("raw_data_column_types.json", "w+", encoding="utf-8") as file:
        file.write(str(column_types))

    with open("dbt_model.txt", "w+", encoding="utf-8") as file:
        model_txt = get_dbt_formatted_sql(column_types, JSON_PREFIX)
        file.write(model_txt)

    print("Successfully completed!")
if __name__ == "__main__":
    main()
```

我们可以像下面这样运行脚本。

`python script.py table_name schema_name nested_identifier`

它将生成一个名为`dbt_model.txt`的输出文件。

```
nested_identifier."col1"::timestamp as col1,
nested_identifier."col2"::varchar as col2,
nested_identifier."col3"::timestamp as col3,
nested_identifier."_extract_timestamp"::timestamp as _extract_timestamp # col4
```

现在，您只需要将输出复制并粘贴到您的 select 语句中。显然，您可以改进脚本来生成 select 语句。我把它留给你，🥲

# 最后

希望它能帮助您有一个生成自动 dbt 模型的想法，并让您免于一行一行地嵌套数据。

诚挚的问候