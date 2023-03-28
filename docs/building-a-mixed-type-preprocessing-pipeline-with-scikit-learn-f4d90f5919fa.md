# 用 scikit-learn 构建混合型预处理流水线

> 原文：<https://medium.com/codex/building-a-mixed-type-preprocessing-pipeline-with-scikit-learn-f4d90f5919fa?source=collection_archive---------7----------------------->

![](img/3397e596d5f4c7c4e4134c1ef177e3ab.png)

来源:[https://specialpipingmaterials.com/](https://specialpipingmaterials.com/)

我可以自豪地承认，在过去的一年里，我对**数据泄露**产生了**恐惧**。虽然这听起来有点不健康，但这种恐惧一直让我保持警惕，并激励我不断检查我的项目中可能的数据泄漏问题。这绝对不是一个坏习惯，但有时会变得费力和疲惫。

今年早些时候，我在做坦桑尼亚水井的三元分类项目。目的是使用其他特征，如记录日期、出资人、安装者、经度、纬度、区域、流域、子村庄、来源、提取类型、付款类型等，将供水点分为三组，即**功能性**、**功能性需求修复**和**非功能性**。我想在训练期间进行交叉验证，以监控模型在看不见的数据上的表现。然而，这也意味着为了避免数据泄露，对于每一轮，我都必须将转换器安装到训练部分，然后使用它们来手动转换训练和验证部分。对于一个包含 39 列混合数据类型(数值型、分类型、日期时间型)的数据集，每一列都需要略微不同的转换方法，并且 k 倍值仅为 5，这将需要大量的重复工作，更不用说在此过程中引入人为错误的风险了。

幸运的是， **scikit-learn** 提供了一种将所有这些步骤无缝集成到管道中的方法。出于演示目的，我将使用相同的坦桑尼亚水点数据集。

# 导入库

```
**from** **sklearn.preprocessing** **import** OneHotEncoder, MinMaxScaler
**from** **sklearn.compose** **import** ColumnTransformer
**from** **sklearn.base** **import** BaseEstimator, TransformerMixin
**from** **sklearn.impute** **import** SimpleImputer
**from** **sklearn.pipeline** **import** Pipeline, make_pipeline, FeatureUnion
**from** **geopy.distance** **import** geodesic
**from** **sklearn.model_selection** **import** cross_val_score
```

# 添加额外功能

## 1.从记录日期提取月/年和年龄记录信息

在探索性数据分析(EDA)过程中，我注意到在所有不同类型的水源中，**雨水收集**没有太多的井，然而`functional`井在该组中的百分比相对较高(60%对平均 53%)。这让我想到，也许井的功能也部分受到检查/测量记录时间的影响。

进一步的调查表明，6 月**日**似乎记录了最高数量的`functional`口井，最低数量的**口干井**口井。这一发现与坦桑尼亚主要的长雨季相吻合，雨季持续于三月、四月和五月。因此，我决定从`date_recorded`中提取记录的月份和年份，以及井的年龄，并将它们转化为单独的特征。

```
**class** **DateTransformer**(BaseEstimator, TransformerMixin):
    *"""Extracts information from date_recorded column to create new features*

 *Returns:*
 *- month_recorded: first 3 character of name of the month recorded*
 *- year_recorded: four-digit year*
 *- age_recorded: difference between construction year and year recorded*
 *"""*

    **def** fit(self, X, y=**None**):
        **return** self

    **def** transform(self, X, y=**None**):
        X['date_recorded'] = pd.to_datetime(X['date_recorded'])
        X['month_recorded'] = X.date_recorded.dt.month_name().str.slice(stop=3)
        X['year_recorded'] = X.date_recorded.dt.year
        X['age_recorded'] = X['year_recorded'] - X['construction_year']
        **return** self
```

## 2.添加关于最近大都市地区的额外要素

井水污染的少数原因之一是来自附近大型住宅区以及工厂、厂房、建筑工地等的污染。虽然比赛本身不允许使用外部数据，但我认为有一些有意义的方法可以将外部数据纳入到改善模型的整体预测能力和可解释性中，特别是当这个数据集中有如此大量的缺失信息时。因此，我决定在我自己的研究中使用外部数据，然后在对比赛的测试数据进行预测时，从管道中移除这种转换。

在获得坦桑尼亚最大城市的数据、地理空间信息和人口后，我挑选出人口超过 100，000 的城市，并将它们保存到一个名为`tz_pop_above100k`的独立数据框架中。

以下函数有助于定位距离给定坐标集最近的大都市区域:

```
**def** closest_point(x):
    *'''*
 *Returns: a tuple*
 *- tuple[0]: name of nearest big city (population over 100000)*
 *- tuple[1]: geodesic distance from point x to nearest big city (tuple[0])*
 *'''*

    output = **None**
    **for** _, city **in** tz_pop_above100k.iterrows():
        distance = geodesic(x, city.coordinates).km
        city_name = city.city
        **if** (output **is** **None**) **or** (distance < output[1]):
            output = (city_name, distance)
    **return** output
```

然后，我将上述函数应用到我的自定义转换器中，以获得每个供水点的名称、距离以及最近大都市的人口:

```
**class** **FeatureGenerator**(BaseEstimator, TransformerMixin):
    *"""Generate extra features from water wells' coordinates*

 *Returns:*
 *- nearest_big_city_name: name of the nearest city with population over 100,000*
 *- nearest_big_city_distance: distance to nearest city with population over 100,000*
 *- nearest_big_city_population: population of nearest big city*
 *"""*

    **def** fit(self, X, y=**None**):
        **return** self

    **def** transform(self, X, y=**None**):
        names = []
        distances = []
        populations = []
        **for** idx, row **in** X.iterrows():
            coordinates = (row['latitude'], row['longitude'])
            n, d = closest_point(coordinates)
            p = tz_pop_above100k[tz_pop_above100k.city == n].population_2021.values[0]
            names.append(n)
            distances.append(d)
            populations.append(p)
        X['nearest_big_city_name'] = names
        X['nearest_big_city_distance'] = distances
        X['nearest_big_city_population'] = populations
        **return** self
```

# 特征预处理

## 1.在某些分类&布尔变量中用字符串`"NaN"`填充缺失值

```
**class** **NaNImputer**(BaseEstimator, TransformerMixin):
    *"""*
 *Replaces missing values np.nan with the string "NaN"*
 *in 3 features 'public_meeting', 'scheme_management', and 'permit'*
 *"""*

    **def** fit(self, X, y=**None**):
        **return** self

    **def** transform(self, X, y=**None**):
        to_fillna = ['public_meeting', 'scheme_management', 'permit']
        X[to_fillna] = X[to_fillna].fillna(value='NaN')
        X[to_fillna] = X[to_fillna].astype(str)  
        **return** X
```

## 2.估算经度

这一步的目标是使用`region_code`将经度中的 0 值替换为区域的聚合平均值。

```
**class** **LongitudeImputer**(BaseEstimator, TransformerMixin):
    *"""*
 *Replaces invalid longitude value (0) with the aggregated means by region*
 *using region_code*
 *"""*
    **def** __init__(self):
        super().__init__()
        self.lng_means_ = {}

    **def** fit(self, X, y=**None**):
        self.lng_means_ = X.groupby(['region_code']).longitude.mean()
        **return** self

    **def** transform(self, X, y=**None**):
        *# replace 0 with average longitude*
        **for** key, value **in** self.lng_means_.items():
            X.loc[((X.longitude == 0) & (X.region_code == key)), 'longitude'] = value
        **return** X
```

## 3.宁滨`funder` & `installer`分为 2 组——大调和小调

这两个变量中的每一个都包含超过 1000 个可能的类别，这意味着如果我将它们全部包含到模型中，在一次热编码后，我将得到超过 4000 个特征。处理这两个变量的高基数的一种方法是将它们宁滨成更大的组:

*   **主要** —前 100 名资助者或安装者——负责用于拟合的数据集中约 80%的水井
*   **次要** —不在 100 强列表中的任何实体

```
**class** **FunderBinner**(BaseEstimator, TransformerMixin):
    *"""*
 *Categorize funders into 2 groups 'major' or minor'*
 *- 'major': top 100 funders in the fitted dataset*
 *- 'minor' - any entities that are not in the top 100 list*
 *"""*

    **def** __init__(self):
        super().__init__()
        self.top_100_funder = []

    **def** fit(self, X, y=**None**):
        self.top_100_funder = X.funder.value_counts()[:100].index
        **return** self

    **def** transform(self, X, y=**None**):
        **for** idx, row **in** X.iterrows():
            **if** row['funder'] **in** self.top_100_funder:
                val = 'major' 
            **else**:
                val = 'minor'
            X.loc[idx, 'funder'] = val
        **return** X**class** **InstallerBinner**(BaseEstimator, TransformerMixin):
    *"""*
 *Categorize installers into 2 groups 'major' or minor'*
 *- 'major': top 100 funders in the fitted dataset*
 *- 'minor' - any entities that are not in the top 100 list*
 *"""*

    **def** __init__(self):
        super().__init__()
        self.top_100_installer = []

    **def** fit(self, X, y=**None**):
        self.top_100_installer = X.installer.value_counts()[:100].index

        **return** self

    **def** transform(self, X, y=**None**):
        **for** idx, row **in** X.iterrows():
            **if** row['installer'] **in** self.top_100_installer:
                val = 'major' 
            **else**:
                val = 'minor'
            X.loc[idx, 'installer'] = val
        **return** X
```

## 4.缩放数字特征

*   缩放经度和纬度在这里特别重要，因为它们在不同的尺度上。
*   在对各种缩放器进行试验后，发现与其他缩放类型(包括`StandardScaler`、`RobustScaler`和`PowerTransformer`)相比，`MinMaxScaler()`产生的结果更好。

```
*# SELECT NUMERICAL FEATURES TO BE INCLUDED IN MODELING*
numerical_features = ['amount_tsh', 'gps_height', 'num_private',
                      'construction_year', 'year_recorded', 
                      'age_recorded', 'population', 
                      'nearest_big_city_population', 'longitude',        
                      'latitude', 'nearest_big_city_distance']numerical_transformer = Pipeline(steps=[('scaler', MinMaxScaler())])
```

## 5.一种热编码分类特征

```
*# SELECT CATEGORICAL FEATURES TO BE INCLUDED IN MODELING*
categorical_features = ['funder', 'installer', 'basin', 'region',
                        'public_meeting', 'scheme_management',
                        'permit', 'extraction_type_class', 
                        'management_group', 'payment_type', 
                        'quality_group', 'quantity', 
                        'source_class', 'waterpoint_type_group', 
                        'nearest_big_city_name', 'month_recorded']categorical_transformer = Pipeline(steps=[
                   ('ohe', OneHotEncoder(handle_unknown='ignore'))
                 ])
```

# 建设管道

一旦我定义了所有的自定义转换器、估算器和缩放器，我只需要将这些部分放入一个管道中，如下所示:

```
*# ADDING NEW FEATURES USING FeatureUnion*
union = FeatureUnion(transformer_list=[ 
            ('recorded',  DateTransformer()),                                             
            ('nearest_big_city', FeatureGenerator())
        ])

*# TRANSFORM NUMERICAL & CATEGORICAL FEATURES SEPARATELY USING ColumnTransformer*
col_tranformer = ColumnTransformer(transformers=[
            ('num', numerical_transformer, numerical_features),
            ('cat', categorical_transformer, categorical_features)
         ],
         remainder='drop')

*# STACKING THEM TOGETHER IN A PIPELINE*
preprocessor = Pipeline(steps=[('fillna', NaNImputer()),
                               ('lng_imp', LongitudeImputer()),
                               ('funder', FunderBinner()),
                               ('installer', InstallerBinner()),
                               ('col_tf', col_tranformer)])
```

## 1.使用带有交叉验证的管道

以下是如何在随机森林分类器的交叉验证中使用管道的示例:

```
*# CREATE NEW FEATURES IN TRAINING SET X_train* union.fit_transform(X_train)*# STACKING preprocessor AND RandomForestClassifier() INTO A PIPELINE*
pipeline = Pipeline(steps=[
              ('preprocessor', preprocessor),
              ('rf', RandomForestClassifier(random_state=2021))
           ])

scores_rf1 = cross_val_score(pipeline, 
                             X_train, y_train, 
                             scoring='accuracy')
```

## 2.通过 SMOTE 使用管道

如果您想使用合成少数过采样技术( **SMOTE** )，请注意`imblearn`有自己的管道，可以通过以下方式导入:

```
**from** **imblearn.pipeline** **import** Pipeline **as** imbPipeline
```

然后我们只需要使用`imbPipeline`而不是`sklearn`的常规`Pipeline`重新创建管道:

```
*# STACKING PREPROCESSOR TRANSFORMATIONS, SMOTE() AND RandomForestClassifier()* 
*# INTO AM IMBALANCE PIPELINE*
pipe_sm = imbPipeline([
            ('fillna', NaNImputer()),
            ('lng_imp', LongitudeImputer()),
            ('funder', FunderBinner()),
            ('installer', InstallerBinner()),
            ('col_tf', col_tranformer),
            ('sampler', SMOTE(random_state=2021, 
                              n_jobs=-1,
                              sampling_strategy='minority')),
            ('rf', RandomForestClassifier(random_state=2021))
          ])
```

`pipe_sm`的使用方法与上述`preprocessor`管道相同:

```
scores_rf2 = cross_val_score(pipe_sm, 
                             X_train, y_train, 
                             scoring='accuracy')
```

有了系统集成的管道，拟合和预测可以像以下一样简单:

```
*# FIT MODEL ON THE TRAINING DATASET X_train and y_train*
rf2 = pipe_sm.fit(X_train, y_train)*# CREATE NEW FEATURES IN VALIDATION SET X_val*
union.fit_transform(X_val)

*# MAKE PREDICTIONS ON THE VALIDATION DATASET* 
y_val_pred = rf2.predict(X_val)
```

# 结论

这只是我如何在项目中为混合特征类型构建预处理管道的一个例子。请注意，这一步通常在彻底的探索性数据分析之后**进行，这为我们提供了如何正确处理每个功能的必要知识。根据数据集以及业务问题，我们可以根据需要毫不费力地调整和修改管道。这种方法还有另一个好处，就是更容易重现。**