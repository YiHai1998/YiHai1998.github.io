# Python连接Hive

###### python连接hive的几种方式

- 基于pyhive连接hive。
- 基于impyla连接hive。

#### 开启metastore和hiveserver2服务

```
hive --service metastore &
hive --service hiveserver2 &12
```

#### beeline调试，远程连接到HiveServer2

```
cd {HIVE_HOME}/bin
./beeline -u connect jdbc:hive2://localhost:10000
```

#### python连接hiveserver2代码

```python
# -*- coding: utf-8 -*-  

import pyhs2  
import sys  

default_encoding = 'utf-8'  
if sys.getdefaultencoding() != default_encoding:  
    reload(sys)  
    sys.setdefaultencoding(default_encoding)  

class HiveClient:  
    def __init__(self,db_host,user,password,database,port=10008,authMechanism="PLAIN"):  
        """ 
        create connection to hive server2 
        """  
        self.conn = pyhs2.connect(host=db_host,  
                                  port=port,  
                                  authMechanism=authMechanism,  
                                  user=user,  
                                  password=password,  
                                  database=database,)  

    def query(self, sql):  

        """ 
        query 
        """  
        with self.conn.cursor() as cursor:  
            cursor.execute(sql)  
            return cursor.fetch()  

    def close(self):  
        """ 
        close connection 
        """  
        self.conn.close()  
if __name__ == '__main__':  
    hive_client = HiveClient(db_host='localhost',port=10000,user='user——name',password='passswd',database='default', authMechanism='PLAIN')  
    sql = "show tables"  
    result = hive_client.query(sql)  
    print result  
    hive_client.close() 
```

