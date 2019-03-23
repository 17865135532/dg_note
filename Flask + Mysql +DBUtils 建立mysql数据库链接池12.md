## Flask + Mysql +DBUtils 建立mysql数据库链接池

参考链接1：https://www.cnblogs.com/wangkun122/articles/8992637.html

参考链接2：https://www.cnblogs.com/supery007/p/8206442.html

参考链接3：https://blog.csdn.net/weixin_39430584/article/details/84104262

参考链接4：https://www.cnblogs.com/ArmoredTitan/p/Flask.html           （项目中使用）

```txt
如果没有连接池，使用pymysql来连接数据库时，单线程应用完全没有问题，但如果涉及到多线程应用那么就需要加锁，一旦加锁那么连接势必就会排队等待，当请求比较多时，性能就会降低了。
```

一、 config.py 配置数据库信息

```
 mysqlInfo= {
             'host':'127.0.0.1',
             'user':'root',
             'passwd':'123456',
             'db':'flask_map',
             'port':3306,
             'charset':'utf8',
 }
```

二、comment/ db.py 里代码

```
import pymysql
from DBUtils.PooledDB import PooledDB
import config

class Database:
   def __init__(self):
      self.host = config.mysqlInfo["host"]
      self.port = config.mysqlInfo['port']
      self.user = config.mysqlInfo['user']
      self.pwd = config.mysqlInfo['passwd']
      self.db = config.mysqlInfo['db']
      self.charset = config.mysqlInfo['charset']
      self.CreatePool()

   #建立链接池对象
   def CreatePool(self):

      self.Pool = PooledDB(creator=pymysql, mincached=5, maxcached=5, maxshared=3, maxconnections=6, blocking=True,host=self.host, port=self.port,user=self.user,
                           password=self.pwd,database =self.db,charset=self.charset)
   
   #链接连接池
   def _Getconnect(self):
      self.conn = self.Pool.connection()
      cur = self.conn.cursor()
      if not cur:
         raise NameError + "数据库连接不上"
      else:
         return cur
   
   #直接调用 con
   def sql_pandas_con(self):
      self.conn = self.Pool.connection()
      
      print('链接被拿走了', self.conn)
      
      return self.conn
   
   # 查询sql
   def ExecQuery(self, sql):
      cur = self._Getconnect()
      cur.execute(sql)
      relist = cur.fetchall()
      cur.close()
      self.conn.close()
      return print(relist)
   
   # 非查询的sql,增删改
   def ExecNoQuery(self, sql):
      cur = self._Getconnect()
      cur.execute(sql)
      self.conn.commit()
      cur.close()
      self.conn.close()
   
   # 显示查询中的第一条记录
   def Showfirst(self, sql):
      cur = self._Getconnect()
      cur.execute(sql)
      resultfirst = cur.fetchone()
      cur.close()
      self.conn.close()
      return print(resultfirst)
   
   # 显示查询出的所有结果
   def Showall(self, sql):
      cur = self._Getconnect()
      cur.execute(sql)
      resultall = cur.fetchall()
      cur.close()
      self.conn.close()
```

```
Pool里面对应的 参数
PooledDB(
        creator=pymysql,  # 使用链接数据库的模块
        maxconnections=6,  # 连接池允许的最大连接数，0和None表示不限制连接数
        mincached=2,  # 初始化时，链接池中至少创建的空闲的链接，0表示不创建
        maxcached=5,  # 链接池中最多闲置的链接，0和None不限制
        maxshared=3,
        # 链接池中最多共享的链接数量，0和None表示全部共享。PS: 无用，因为pymysql和MySQLdb等模块的 threadsafety都为1，所有值无论设置为多少，_maxcached永远为0，所以永远是所有链接都共享。
        blocking=True,  # 连接池中如果没有可用连接后，是否阻塞等待。True，等待；False，不等待然后报错
        maxusage=None,  # 一个链接最多被重复使用的次数，None表示无限制
        setsession=[],  # 开始会话前执行的命令列表。如：["set datestyle to ...", "set time zone ..."]
        ping=0,
        # ping MySQL服务端，检查是否服务可用。# 如：0 = None = never, 1 = default = whenever it is requested, 2 = when a cursor is created, 4 = when a query is executed, 7 = always
        host='127.0.0.1',
        port=3306,
        user='root',
        password='123456',
        database='s8day127db',#链接的数据库的名字
        charset='utf8'
    )
```



```
		try:
			sql_map = "SELECT rat_method_trdlev, ratmatrix_indicator, ratmatrix_repindicator, indicator_class, score, maplanguage FROM inddata_industrymaplanguageinfo where rat_method_trdlev in  ( " + ','.join(
				ind_list_a) + ")"
			map_score_data = pd.read_sql(sql_map, self.coon_pool)
		except Exception as e:
			print(e)
		#添加关闭
		finally:
			if self.coon_pool:
				self.coon_pool.close()
```









