---
date : 2026-04-09
tags: ['2026-04']
categories: ['kosmos']
bookHidden: true
title: "Netezza DB 접속"
pageHidden: true
---

# Netezza DB 접속

#2026-04-09

---


```python
import pandas as pd
import pandas_profiling
import pyodbc

def profiling_cnrg_table(db_name,cancer_name):

  netzza_conn = pyodbc.connect("DRIVER={NetezzaSQL};SERVER=10.32.29.203;PORT=5480;DATABASE="+str(db_name)+";UID=crdwadmin;PWD=crdwadmin;")
  tablelist_sql = 'select TABLENAME from _v_table where OBJCLASS=4905 and tablename like ?'
  #tablename_df = pd.read_sql_query(tablelist_sql, netzza_conn)
  tablename_df = pd.read_sql(tablelist_sql, netzza_conn,params={str(cancer_name)})

  for i in tablename_df.index:
    tablename = tablename_df.at[i,'TABLENAME']
    select_sql = 'select ATTNAME from _v_relation_column where OBJCLASS=4905 and ATTCOLLENG <500 and name like ?'
    select_df = pd.read_sql(select_sql, netzza_conn,params={str(tablename)})
    profile_sql = 'SELECT '+','.join(select_df['ATTNAME'].to_list())+' FROM ' +str(tablename)
    print(tablename)

    #profile_sql = 'select * from '+str(tablename)
    colname_sql = "select ATTNAME from _v_relation_column where OBJCLASS=4905 and ATTTYPID =1082 and name like ?"
    colname_df = pd.read_sql(colname_sql, netzza_conn,params={str(tablename)})
    profile = pd.read_sql_query(profile_sql, netzza_conn)
    profile[colname_df['ATTNAME'].to_list()] =profile[colname_df['ATTNAME'].to_list()].apply(pd.to_datetime,format = '%Y/%m/%d',errors='coerce')
    profile.profile_report(minimal=True).to_file('D:/source/crdw/'+str(tablename)+'.html')

    del [[profile,colname_df]]
```
```python
profiling_cnrg_table('CNRG','KDNY%')
```
```python
netzza_conn = pyodbc.connect("DRIVER={NetezzaSQL};SERVER=10.32.29.203;PORT=5480;DATABASE="+str("CNRG")+";UID=crdwadmin;PWD=crdwadmin;")
tablelist_sql = 'select TABLENAME from _v_table where OBJCLASS=4905 and tablename like ?'
#tablename_df = pd.read_sql_query(tablelist_sql, netzza_conn)
pd.read_sql(tablelist_sql, netzza_conn,params={str("BRST%")})
```