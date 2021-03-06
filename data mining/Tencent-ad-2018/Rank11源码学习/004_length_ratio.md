# 004_length_ratio

模型功能:

多值特征值的个数百分比值, 兴趣\关键字\话题\app, 比如:interest1字段取值个数, 以及其占总的interest1~5所有字段取值个数之和的比值.

输入:

- data_preprocessing/train_test_merge.csv

输出:

- 添加有新的特征的训练集/验证集/测试集1/测试集2

```python
import pandas as pd  #添加有新的特征
import lightgbm as lgb
from sklearn.model_selection import train_test_split
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.preprocessing import OneHotEncoder,LabelEncoder
from scipy import sparse
import os
import numpy as np
import time
import random
import warnings
warnings.filterwarnings("ignore")
##读取数据
print("Reading...")
data = pd.read_csv('data_preprocessing/train_test_merge.csv')
##划分训练与测试集
print('Index...')
train_part_index = list(data[(data['label']!=-1)&(data['n_parts']!=1)].index)
evals_index = list(data[(data['label']!=-1)&(data['n_parts']==1)].index)
test1_index = list(data[data['n_parts']==6].index)
test2_index = list(data[data['n_parts']==7].index)

vec_feature = ['creativeSize','ct','marriageStatus','interest1', 'interest2', 'interest3', 
               'interest4','interest5', 'kw1', 'kw2','kw3', 'topic1', 'topic2', 
               'topic3','appIdAction', 'appIdInstall']
data = data[vec_feature]
df_feature = data['creativeSize']
del data['creativeSize']

df_feature = df_feature.astype(int)
value = df_feature.values

df_feature = pd.DataFrame()
df_feature['creativeSize'] = value

for co in vec_feature[1:]:
    print(co)
    s = time.time()
    value = []
    lis = list(data[co].values)
    for i in range(len(lis)):
        value.append(len(lis[i].split(' '))) #值的个数
    col_name = co+'_length'
    df_feature[col_name]=value
    print(co,int(time.time()-s),'s')
    del data[co]
print('interests...')
df_feature['interests_length'] = df_feature['interest1_length']+df_feature['interest2_length']+df_feature['interest3_length']+df_feature['interest4_length']+df_feature['interest5_length']
interests = ['interest1', 'interest2', 'interest3', 'interest4','interest5']
for co in interests:
    col_name = 'ratio_of_'+co
    df_feature[col_name] = (df_feature[co+'_length']/df_feature['interests_length']*100).astype(int) #各字段占比*100
print('kws...')
df_feature['kws_length'] = df_feature['kw1_length']+df_feature['kw2_length']+df_feature['kw3_length']
kws = ['kw1', 'kw2', 'kw3']
for co in kws:
    col_name = 'ratio_of_'+co
    df_feature[col_name] = (df_feature[co+'_length']/df_feature['kws_length']*100).astype(int)
print('topics...')
df_feature['topics_length'] = df_feature['topic1_length']+df_feature['topic2_length']+df_feature['topic3_length']
topics = ['topic1', 'topic2', 'topic3']
for co in topics:
    col_name = 'ratio_of_'+co
    df_feature[col_name] = (df_feature[co+'_length']/df_feature['topics_length']*100).astype(int)

print('apps...')
df_feature['apps_length'] = df_feature['appIdAction_length']+df_feature['appIdInstall_length']
apps = ['appIdAction', 'appIdInstall']
for co in apps:
    col_name = 'ratio_of_'+co
    df_feature[col_name] = (df_feature[co+'_length']/df_feature['apps_length']*100).astype(int)

data = []
train_part_x = df_feature.loc[train_part_index]
evals_x = df_feature.loc[evals_index]
test1_x = df_feature.loc[test1_index]
test2_x = df_feature.loc[test2_index]
df_feature = []
train_part_y = pd.read_csv('data_preprocessing/train_part_y.csv',header=None)
evals_y = pd.read_csv('data_preprocessing/evals_y.csv',header=None)

col_new = ['creativeSize', 'interest2_length', 'ratio_of_interest2',
       'interest1_length', 'ratio_of_interest1', 'ct_length',
       'marriageStatus_length', 'interests_length', 'ratio_of_interest5',
       'interest5_length', 'kw2_length', 'ratio_of_kw1', 'ratio_of_interest4',
       'topics_length']
print('Saving...')
print('train_part...')
train_part_x[col_new].to_csv('data_preprocessing/train_part_x_length.csv',index=False)
print('evals...')
evals_x[col_new].to_csv('data_preprocessing/evals_x_length.csv',index=False)
print('test1...')
test1_x[col_new].to_csv('data_preprocessing/test1_x_length.csv',index=False)
print('test2...')
test2_x[col_new].to_csv('data_preprocessing/test2_x_length.csv',index=False)
print('Over')
```

