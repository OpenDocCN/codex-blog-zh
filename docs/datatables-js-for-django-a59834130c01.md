# Django 的 DataTables.js

> 原文：<https://medium.com/codex/datatables-js-for-django-a59834130c01?source=collection_archive---------1----------------------->

## 在 Django 中实现 AJAX 数据表的 Python 类

你好。在这篇文章中，我不打算一步一步地写所有的东西。我不会一步一步地编写，而是用几行代码解释我是如何实现使用 DataTables.js 创建的动态数据表的。

![](img/99ecc582ccf54362bfdd8aaa19beb513.png)

照片由[米卡·鲍梅斯特](https://unsplash.com/@mbaumi?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

我创建了一个 repo 来共享示例代码。您可以使用下面的链接访问示例。

[](https://github.com/mebaysan/Django-DataTable) [## GitHub-mebaysan/Django-DataTable:Django 的数据表 js

### 我创建了这个 repo 来与社区共享我的代码:DataTable.js 在 repo 中，你可以找到要实现的类…

github.com](https://github.com/mebaysan/Django-DataTable) 

# 数据表工厂

感谢这个类，我们可以很容易地获取合适的 DataTableFactory 类。默认情况下，我为 AWS 连接创建了一个工厂类。您可以用这个类直接显示您的 SQL 数据。

```
import pandas as pd
from .factories.aws_dtfactory import AWSDataTableFactory

class DataTableFactory(object):
    """Data Table Factory class to generate a related sub data table factory class

    """

    @staticmethod
    def get_factory(query_engine):
        """Get a query_engine related data table factory object

        Args:
            query_engine (str): [aws] To get related factory class
        Returns:
            an object that is created by using query_engine related data table factory class
        """
        if query_engine == "aws":
            return AWSDataTableFactory(query_engine)
        else:
            return pd.DataFrame()
```

# AWS 数据表工厂

通过使用这个类，我们可以很容易地用 AJAX 过滤器参数过滤查询的数据。如果想深入考察过滤方法，可以看看`filter_by_request_args`法。

```
from sqlalchemy import create_engine
import pandas as pd

def get_redshift_con():
    user = ""
    password = ""
    host = ""
    port = ""
    database = ""
    return create_engine(
        f'postgresql+psycopg2://{user}:{password}@{host}:{port}/{database}'
    )

class AWSDataTableFactory(object):
    def __init__(self, query_engine):
        """Create an AWSDataTableFactory object

        AWS specific data table data generator
        Args:
            query_engine (str): "aws"
        """
        self.query_engine = query_engine
        self.query = None
        self.columns = []
        self.displayed_columns = []

    def set_query(self, query):
        """Set factory's query

        Args:
            query (str): The query will be executed

        Returns:
            It doesn't return anything. It sets the factory's query.
        """
        self.query = query
        self.set_columns()

    def set_columns(self):
        """Set factory's columns

        Returns:
            It doesn't return anything. It sets the factory's columns.
        """
        limited_query = self.query.split('limit')[0]
        limited_query += " limit 1"
        res = self.get_data(limited_query)
        self.columns = [col for col in res.columns]
        self.displayed_columns = self.columns[:4]

    def set_displayed_columns(self, column_list):
        """Set factory's displayed columns

        Args:
            column_list (list): List of columns
        """
        self.displayed_columns = column_list

    def get_displayed_columns_for_data_table(self):
        """Get data table columns for AJAX request

        Returns:
            list: List of dictionaries
        """
        return [{'data': col} for col in self.displayed_columns]

    def get_data(self, query=None, *args, **kwargs):
        """Get (extract) data from the source by executing the query with the engine

        Args:
            query (str): The query will be executed
            *args:
            **kwargs:

        Returns:
            table_data (pd.DataFrame): Returns the data that extracted from AWS
        """
        if self.query is None:
            raise NotImplementedError(
                'Firstly you have to set a query for the engine')
        con = get_redshift_con()
        if query is None:
            table_data = pd.read_sql(con=con, sql=self.query, *args, **kwargs)
        else:
            table_data = pd.read_sql(con=con, sql=query, *args, **kwargs)
        return table_data

    def filter_by_request_args(self, **kwargs):
        """Filter and sort the data by using request.GET parameters

        Args:
            **kwargs: request.GET

        Returns:
            dict: to use in the frontend with ajax data table requests
        """
        draw = int(kwargs.get('draw', None)[0])
        length = int(kwargs.get('length', None)[0])
        start = int(kwargs.get('start', None)[0])
        search_value = kwargs.get('search[value]', None)[0]
        order_column = kwargs.get('order[0][column]', None)[0]
        order = kwargs.get('order[0][dir]', None)[0]  # asc or desc
        factory_filters = [f for f in kwargs if f.startswith('factory_filter')]
        order_column = self.columns[int(order_column)]
        queryset = self.get_data()

        ############ factory_filters applying ############
        if len(factory_filters) > 0:
            for ff in factory_filters:
                query_col = ff.split('factory_filter-')[1]
                query_val = kwargs.get(ff)[0]
                if query_val != 'All':
                    queryset = queryset[queryset[query_col] == query_val]

        total = len(queryset)
        if search_value:
            queryset = queryset[queryset.apply(lambda row: row.astype(
                str).str.contains(search_value).any(), axis=1)]

        count = len(queryset)

        queryset.sort_values(
            by=order_column, ascending=False if order == 'desc' else True, inplace=True)

        queryset = queryset[self.displayed_columns]

        return {
            'data': queryset,
            'count': count,
            'total': total,
            'draw': draw,
            'start': start,
            'length': length
        }
```

# 数据表视图

这是我们基于类的观点。在这个类中，我们使用来自`urls.py`的参数。

```
from django.shortcuts import render
from django.contrib.auth.decorators import login_required
from django.views import View
from .utilities.dtfactory import DataTableFactory
from django.utils.decorators import method_decorator
from django.core.exceptions import PermissionDenied
from django.http import JsonResponse

@method_decorator(login_required, name="dispatch")
class DataTableView(View):
    """Data table class based view
    This view can get the parameters below in the as_view function  to use in the view
    Args:
        page_name (str): To display in page
        query_engine (str): [aws] To create a DataTableFactory for querying the data source
        query (str): To execute the query by using the connection that is created with query_engine
        query_parameters (dict): Additional parameters to pass into to exec_sql method of the factory
        permission (str): The needed permission to access the url
    """
    page_name = ""
    query_engine = ""
    query = ""
    query_parameters = {}
    permission = ""
    filters = []

    def get(self, request, *args, **kwargs):
        # todo: kolon seçimi eklenecek
        data_table_factory = DataTableFactory.get_factory(self.query_engine)
        data_table_factory.set_query(self.query)

        if request.GET.get('ajax_factory_loader', None) is not None:
            filter_args = data_table_factory.filter_by_request_args(
                **request.GET)
            data = filter_args['data'].to_dict(orient='records')
            data = data[filter_args['start']                        :filter_args['start'] + filter_args['length']]
            result = {'data': data, 'draw': filter_args['draw'], 'recordsTotal': filter_args['total'],
                      'recordsFiltered': filter_args['count']}
            return JsonResponse(result)
        return render(
            request,
            "datagateway/data_table.html",
            context={"page_name": self.page_name,
                     'filters': self.filters, 'factory': data_table_factory},
        )

    def dispatch(self, request, *args, **kwargs):
        if not request.user.has_perm(self.permission):
            raise PermissionDenied(
                "You do not have permission to view this page")
        return super().dispatch(request, *args, **kwargs)
```

# urls.py

在这个文件中，我们传递在基于类的视图中使用的参数。我们可以定义所需的权限、查询引擎、查询和静态过滤器等。

```
from django.urls import path
from . import views

app_name = "dtfactory"

urlpatterns = [
    path(
        "",
        views.DataTableView.as_view(
            page_name="Data Table Demo",
            query_engine="aws",
            query=f"select * from QUERY",
            permission="custom_app.can_view_data_table_page",
            filters=[
                {'filter_column': 'custom_region', 'display_name': 'Region', 'type': 'static',
                 'filter_values': ['All', 'TR', 'EN']}
            ]
        ),
        name="dt_demo",
    ),
]
```

# 模板文件

在这个文件中，我们使用来自后端的工厂参数。

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Data Table Demo</title>
    <link
      rel="stylesheet"
      href="cdn.datatables.net/1.13.1/css/jquery.dataTables.min.css"
    />
  </head>

  <body>
    <table id="demoDT">
      <thead>
        <tr>
          {% for col in factory.displayed_columns %}
          <th>{{ col }}</th>
          {% endfor %}
        </tr>
      </thead>

      <tbody></tbody>
    </table>

    <script src="cdn.datatables.net/1.13.1/js/jquery.dataTables.min.js"></script>
    <script>
      $(document).ready(function () {
          function customExportAction(e, dt, button, config) {
              var self = this;
              var oldStart = dt.settings()[0]._iDisplayStart;
              dt.one('preXhr', function (e, s, data) {
                  // Just this once, load all data from the server...
                  data.start = 0;
                  data.length = 2147483647;
                  dt.one('preDraw', function (e, settings) {
                      // Call the original action function
                      if (button[0].className.indexOf('buttons-copy') >= 0) {
                          $.fn.dataTable.ext.buttons.copyHtml5.action.call(self, e, dt, button, config);
                      } else if (button[0].className.indexOf('buttons-excel') >= 0) {
                          $.fn.dataTable.ext.buttons.excelHtml5.available(dt, config) ?
                              $.fn.dataTable.ext.buttons.excelHtml5.action.call(self, e, dt, button, config) :
                              $.fn.dataTable.ext.buttons.excelFlash.action.call(self, e, dt, button, config);
                      } else if (button[0].className.indexOf('buttons-csv') >= 0) {
                          $.fn.dataTable.ext.buttons.csvHtml5.available(dt, config) ?
                              $.fn.dataTable.ext.buttons.csvHtml5.action.call(self, e, dt, button, config) :
                              $.fn.dataTable.ext.buttons.csvFlash.action.call(self, e, dt, button, config);
                      } else if (button[0].className.indexOf('buttons-pdf') >= 0) {
                          $.fn.dataTable.ext.buttons.pdfHtml5.available(dt, config) ?
                              $.fn.dataTable.ext.buttons.pdfHtml5.action.call(self, e, dt, button, config) :
                              $.fn.dataTable.ext.buttons.pdfFlash.action.call(self, e, dt, button, config);
                      } else if (button[0].className.indexOf('buttons-print') >= 0) {
                          $.fn.dataTable.ext.buttons.print.action(e, dt, button, config);
                      }
                      dt.one('preXhr', function (e, s, data) {
                          // DataTables thinks the first item displayed is index 0, but we're not drawing that.
                          // Set the property to what it was before exporting.
                          settings._iDisplayStart = oldStart;
                          data.start = oldStart;
                      });
                      // Reload the grid with the original page. Otherwise, API functions like table.cell(this) don't work properly.
                      setTimeout(dt.ajax.reload, 0);
                      // Prevent rendering of the full data to the DOM
                      return false;
                  });
              });
              // Requery the server with the new one-time export settings
              dt.ajax.reload();
          }

          $("#demoDT").DataTable({
                  processing: true,
                  serverSide: true,
                  lengthChange: true,
                  ajax: {
                      url: "{{ request.get_full_path }}", // send ajax request to itself for filtering and paginating the data
                       data: {ajax_factory_loader:true},
                  },
                  columns: {{ factory.get_displayed_columns_for_data_table | safe }},
                  "dom": 'Blfrtip',
                  "buttons": [
                      {
                          extend: 'excel',
                          text: 'Excel',
                          filename: "{{page_name}}",
                          header: true,
                          title: "{{page_name}}",
                          "action": customExportAction
                      },
                      {
                          extend: 'csv',
                          text: 'CSV',
                          filename: "{{page_name}}",
                          header: true,
                          title: "{{page_name}}",
                          "action": customExportAction
                      },
                      {
                          extend: 'pdf',
                          text: 'PDF',
                          filename: "{{page_name}}",
                          header: true,
                          title: "{{page_name}}",
                          "action": customExportAction
                      },
                  ],
              })
              .buttons()
              .container()
              .appendTo("#download-action-area")
      });
    </script>
  </body>
</html>
```

# 最后

希望它能帮助您在 Django 项目中使用 DataTables.js。我知道它太短了。不过，我相信你只要看一眼就能理解代码。如果你想扩展工厂，你应该在`DataTableFactory`类中实现你自己的工厂。

问候