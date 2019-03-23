1、实例化

```
from flask_cache import Cache

#初始化
app = Flask(__name__)
#redis建立缓冲的操作
cache = Cache()
config = {
  'CACHE_TYPE': 'redis',
  'CACHE_REDIS_HOST': '127.0.0.1',
  'CACHE_REDIS_PORT': 6379,
  'CACHE_REDIS_DB': '',
  'CACHE_REDIS_PASSWORD': ''
}

app.config.from_object(config)
cache.init_app(app)
```

```\

self.redis_conn = Redis.connect()


@app.route("/v1/get/map_list/score", methods=["POST"])
# """多指标的映射语言， 涉及括号的问题"""
def language_list():
	data = request.get_json()
	if not data:
		data = request.form
	print("参入多指标的映射语言 %s " % data)
	a = MyRequestAction()
	result = a.map_list_content(data, cache)
	print("涉及到括号指标输出%s" % result)
	return jsonify({"result_data": json.dumps(result, ensure_ascii=False)})
```

```
#建立key值
redis_key = industry + show_field + score_ro
		print("redis key == >  %s" % redis_key)
		print(type(redis_key))
		
		# result_dict = self.redis_conn.get(redis_key)
		#查询key值是否存在， 存在值直接返回
		result_dict = Redis.get_data(self.redis_conn, redis_key)
		
		if result_dict:
			print("缓冲数据")
			print("redis 数据库拿到的数据== >  %s" % result_dict)
			return result_dict
		
		map_score_data = pd.DataFrame()
		if industry:
			ind_list = ["所有行业"]
			ind_list.append(industry)
			ind_list_a = ["'" + i + "'" for i in ind_list]
		else:
			return {'error': "No行业"}
		
		try:
			sql_map = "SELECT rat_method_trdlev, ratmatrix_indicator, ratmatrix_repindicator, indicator_class, score, maplanguage FROM inddata_industrymaplanguageinfo where rat_method_trdlev in  ( " + ','.join(
				ind_list_a) + ")"
			# print(sql_map)
			map_score_data = pd.read_sql(sql_map, self.conn_mysql_b)
			print("指标等级数据大小{}".format(map_score_data.shape))
		except Exception as e:
			print(e)
		if map_score_data.empty:
			# return {"error":"该行业下没有数据"}
			return {"result": "-"}
		result_dict = self.launage__list_(map_score_data, show_field, score_ro, industry)
		# self.redis_conn.set(redis_key, result_dict, ex=10)
		
		#不存在就设置key值入库
		Redis.set_data(self.redis_conn, redis_key, result_dict, ex=100)
		# a = Redis.get_data(self.redis_conn, redis_key)
		# print("redis 数据 a %s" % a)
		return result_dict
	
```

