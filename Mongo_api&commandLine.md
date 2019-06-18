

cls:清屏

> show dbs; 查询所有的数据库

> use komablog; 切换数据库,如果不存在则创建。
>创建后 db 显示当前数据库

> show collections; 查询集合

> db.createCollection("posts"); 创建集合

>db.posts.drop();	//删除集合

> db.stats(); #当前数据库的配置

> db.users.renameCollection("staff");  重命名集合users 以前集合名

> db.staff.drop();	删除指定集合

> db.dropDatabase(); 删除当前数据库

> db.posts.find();  查询集合的所有数据

> db.posts.count(); 查询文档条数

> db.posts.remove({});	集合中所有数据置空


#查询条件

> db.posts.insert({title:"怪物猎人世界评测","rank":2,"tag":"game"});

> db.posts.find({"tag": "game"});	条件查询
> db.posts.find({"rank": {$gte: 4}});
> db.posts.find({"rank": {$gt: 4}});
> db.posts.find({"rank": {$lte: 4}});
> db.posts.find({"rank": {$lt: 4}});
> db.posts.find({"title": /u/});
> db.posts.find({"title": /^R/});
> db.posts.find({"title": /^U/});

> db.posts.distinct("tag"); 查询并去重字段

#复杂条件查询文档

> db.posts.find({"title": /u/, "rank":{$gte:5} });  	#and条件
> db.posts.find({$or: [{"title": /u/}, {"rank":{$gte:4}}] }); # or 条件 
> db.posts.find({"rank": {$in: [3,4]} }); # in 条件
> db.posts.insert({ "title":"惊！骑士发生重大交易", "istop": true }); #没有的字段新增

> db.posts.find({"istop": {$exists: true} });	#判断是否存在

> db.orders.find({"onumber":{$in:["008","009"]}},{books:{$slice:1}})	$slice查询数组中指定返回元素的个数

> db.orders.find({"onumber":{$in:["008","009"]}},{books:{$slice:[1,1]}})	 $slice可以查询数组中第几个到第几个

>db.orders.find({"items":{$elemMatch:{"quantity":2}}})  或者 db.orders.find({"items.quantity":2})

> db.orders.find({"items":{$elemMatch:{"quantity":4,"ino":"002"}}})  多个条件

	

#指定抽出字段

> db.posts.find({}, {title:true, rank:1});	#1:降序 0:升序 find()中第二个对象是 要抽取的字段

> db.posts.find({}, {title:true, rank:1, _id:0}); # 1:true 、0:false


# 排序、分页、跳过

> db.posts.find({}, {_id:0}).sort({rank:1});
> db.posts.find({}, {_id:0}).sort({rank:-1});
> db.posts.find({}, {_id:0}).limit(3);
> db.posts.find({}, {_id:0}).sort({rank:-1}).limit(3);

> db.posts.findOne({}, {_id:0});	#只查询一条

> db.posts.find({}, {_id:0}).skip(3).limit(3);

# 文档更新（update）

$ mongo
> db.posts.findOne({"title":"怪物猎人世界评测"});

> db.posts.update({"title":"怪物猎人世界评测"}, {$set: {"rank": 10} });

> db.posts.update({"title":"怪物猎人世界评测"}, {"rank": 99}); #这个语法不对，会把整个文档更新

> db.posts.update({"tag":"it"}, {$set: {"rank": 50}});	#默认更新一条符合的数据

> db.posts.update({"tag":"it"}, {$set: {"rank": 60}}, {multi: true}); #更新多条符合的数据


#文档的特殊更新

	# upsert:有则更新，无则追加
	# remove:条件删除数据
	
> db.posts.update({title:"其实创造比大志好玩"}, {title:"其实创造比大志好玩", "rank":5,"tag":"game"});

> db.posts.update({title:"其实创造比大志好玩"}, {title:"其实创造比大志好玩", "rank":5,"tag":"game"}, {upsert:true});

> db.posts.update({title:"其实创造比大志好玩"}, {title:"其实创造比大志好玩", "rank":7,"tag":"game"}, {upsert:true});

> db.posts.remove({title:"其实创造比大志好玩"});

	
#特殊函数

$inc:递加
$mul:相乘
$rename:改名
$set:新增or修改
$unset:字段删除

> db.posts.update({title:"怪物猎人世界评测"}, {$inc: {rank: 1}});
> db.posts.find({title:"怪物猎人世界评测"}, {_id:0});
> db.posts.update({title:"怪物猎人世界评测"}, {$mul: {rank: 2}});
> db.posts.find({title:"怪物猎人世界评测"}, {_id:0});
> db.posts.update({title:"怪物猎人世界评测"}, {$rename: {"rank": "score"}});
> db.posts.find({title:"怪物猎人世界评测"}, {_id:0});
> db.posts.update({title:"怪物猎人世界评测"}, {$set: {"istop": true}});
> db.posts.find({title:"怪物猎人世界评测"}, {_id:0});
> db.posts.update({title:"怪物猎人世界评测"}, {$unset: {"istop": true}});
> db.posts.find({title:"怪物猎人世界评测"}, {_id:0});

#使用索引

$ mongo
> db.posts.getIndexes();
> db.posts.createIndex({rank:-1});

> db.posts.dropIndex({rank:-1});
> db.posts.createIndex({title:1}, {unique:true}); #唯一索引

#备份和恢复

$ mongo
> exit
$ mkdir dbbak
$ cd dbbak
$ mongodump -d komablog
$ ls
$ mongo komablog
$ mongorestore --drop
$ mongo komablog
$ mongodump --help


----------------------Java-Code-Api----------------------

在配置文件中指定数据库的名称


#查询基本使用姿势
	
	Query query = new Query(Criteria.where("user").is("一灰灰blog"));
	
	// 查询一条满足条件的数据
	Map result = mongoTemplate.findOne(query, Map.class, COLLECTION_NAME); //返回值可以收Map 或者JsonObject
	
	// 满足所有条件的数据
	List<Map> ans = mongoTemplate.find(query, Map.class, COLLECTION_NAME);
	
	
	//多个查询条件同时满足
	Query query = new Query(Criteria.where("user").is("一灰灰blog").and("age").is(18));
	
	// 等同于 db.getCollection('demo').find({"user": "一灰灰blog", $or: [{ "age": 18}, { "sign": {$exists: true}}]})
	
	先and 条件再 or（ a=x and b=x）
    Query query = new Query(Criteria.where("user").is("一灰灰blog")
            .orOperator(Criteria.where("age").is(18), Criteria.where("sign").exists(true)));
   
    // 单独的or查询  a=x and b=x
    // 等同于Query: { "$or" : [{ "age" : 18 }, { "sign" : { "$exists" : true } }] }, Fields: { }, Sort: { }
    query = new Query(new Criteria().orOperator(Criteria.where("age").is(18), Criteria.where("sign").exists(true)));
	
	
	//in查询
    Query query = new Query(Criteria.where("age").in(Arrays.asList(18, 20, 30)));

	//数字类型，比较查询 >
	Query query = new Query(Criteria.where("age").gt(18));
	
	//正则查询
	Query query = new Query(Criteria.where("user").regex("^一灰灰blog"));
	
	//聚合查询:分组查询
	Aggregation aggregation = Aggregation.newAggregation(Aggregation.group("user").count().as("userCount"));
    AggregationResults<Map> ans = mongoTemplate.aggregate(aggregation, COLLECTION_NAME, Map.class);
    
	//排序查询 sort查询条件，需要用with来衔接
	Query query = Query.query(Criteria.where("user").is("一灰灰blog")).with(Sort.by("age"));
	

	// limit限定查询2条
	Query query = Query.query(Criteria.where("user").is("一灰灰blog")).with(Sort.by("age")).limit(2);
	
	// skip()方法来跳过指定数量的数据
    query = Query.query(Criteria.where("user").is("一灰灰blog")).with(Sort.by("age")).skip(2);
	
	#指定返回字段
	Field fields = query.fields(); 
    fields.include("name"); //要返回的字段
    fields.exclude("_id");	//不要返回的字段
	
	
#新增文档使用姿势

	JSONObject object = new JSONObject();
    object.put("name", "一灰灰blog");

    // 插入一条document
    mongoTemplate.insert(object, COLLECTION_NAME); //sava
	
	// 批量插入文档 将多条文档放入集合当中 和一条插入一样，因为插入的是集合
	List<Map<String, Object>> records = new ArrayList<>();
    
	update();如果查询到了数据，有新的字段会新增字段
	
	update();如果没查询到了数据，数据不会新增
	
	//upsert不存在才插入，根据query条件查询，没有查到，会新增数据
	Query query = new Query();
    query.addCriteria(Criteria.where("_id").is(1000));
	Update update = new Update();
	update.set("_id", 1000);
	update.set("name", "hahah");
	UpdateResult insert2 = mongoTemplate.upsert(query,update,User.class);
	
	//upsert多条满足时, 两条数据匹配时，upsert 将只会更新一条数据
	
	//updateMulti 更新满足条件的多条数据
	
	
#修改基本使用姿势

	//直接修改值的内容
	
	//直接修改值的内容
    Query query = new Query(Criteria.where("_id").is("5c49b07ce6652f7e1add1ea2"));

    Update update = new Update().set("desc", "Java & Python Developer");
    mongoTemplate.updateFirst(query, update, COLLECTION_NAME);
	
	//field修,field可以增加、删除和重命名，
	
	//a. 重命名:当修改的docuemnt没有这个成员时，相当于没有任务操作
	Update update = new Update().rename("desc", "skill");
	
	
	//b.新增成员和下面的效果一样 	
	#新增文档使用姿势 update，新增也是直接利用的Update#set方法，当存在时，修改；不存在时，添加
	Update update = new Update().set("new-skill", "Python");
	
	//c. 删除成员
	// 删除字段，如果不存在，则不操作
    Update update = new Update().unset("new-skill");
	
	
	#函数
	//数字增加/减少
	Update numUp = new Update().inc("age", 20L);
	//数字比较修改
	Update cmpUp = new Update().max("age", 88);
	// 乘法
	Update mulUp = new Update().multiply("age", 3);	
	// 日期修改
	Update dateUp = new Update().currentDate("date");

#-------------------------------

	#3.数组操作 
	//更新文档中字段为数组成员的值
	
	Query query = new Query(Criteria.where("_id").is("5c6a7ada10ffc647d301dd62"));//查询条件
	
	//a. 添加到数组中,在数组中新增一个数据，提供了两种方式
	
		(1):query.Update#addToSet(java.lang.String, java.lang.Object)	//不能插入重复数据
			
			Update update = new Update().addToSet("add", insert);
			mongoTemplate.updateFirst(query, update, COLLECTION_NAME);
		
		(2):query.Update#push(java.lang.String, java.lang.Object)；	//可以插入重复数据
			
			update = new Update().push("add", 10);
			mongoTemplate.updateFirst(query, update, COLLECTION_NAME);

	//b. 批量添加
	
		//一次添加多个，借助addToSet的each来实现
		
		// 批量插入数据到数组中, 注意不会将重复的数据丢入mongo数组中
		Update update = new Update().addToSet("add").each("2", "2", "3");
		
	//c. 删除
	
		//借助pull来精确删除某个值
		
		Update update = new Update().pull("add", "2");
		mongoTemplate.updateFirst(query, update, COLLECTION_NAME);

	//d. 修改
	
		#修改,首先的问题是要定位,确定删除数组中某个下标的元素，这里借助了一个有意思的站位

		# 定位删除的数组元素方法： arrayKey.index
		# arrayKey 是数组在docment中的名
		# index 表示要删除的索引
		
		// 使用set，field.index 来更新数组中的值
		// 更新数组中的元素，如果元素存在，则直接更新；如果数组个数小于待更新的索引位置，则前面补null
		Update update = new Update().set("add.1", "updateField");
		mongoTemplate.updateFirst(query, update, COLLECTION_NAME);

		update = new Update().set("add.10", "nullBefore");
		mongoTemplate.updateFirst(query, update, COLLECTION_NAME);
			
	demo:
	{
      "_id" : ObjectId("5c6a7ada10ffc647d301dd62"),
      "age" : 28.0,
      "name" : "一灰灰blog",
      "skill" : "Java Developer",
      "add":
		[
			额外增加, 新添加>>1550489696892, 
			10, 
			2, 
			3
		], 
      "date" : ISODate("2019-01-28T08:00:08.373Z"),
      "doc" : {
          "key" : "小目标",
          "value" : "升职加薪，迎娶白富美"
      }
	}

	
	


注意事项：
	
	1.当进行Update 操作的时候，如果没有设置值
	
		Update update = new Update();
	
		没有进行 update.set("key","value"); 则 程序认为 是 update = {}; 会将本文档对象置空，只剩下_id
	
	2.更新某个数组中的对象的值
	
		demo:
		{
		  "_id" : ObjectId("5c6a7ada10ffc647d301dd62"),
		  "age" : 28.0,
		  "groups":
			[
				{	
					"group_id":001,
					"attr":[
						"group_name":"物联网组",
						"group_money":"10000"
					]
					
				},
				{	
					"group_id":002,
					"attr":[
						"group_name":"物联网组",
						"group_money":"10000"
					]
					
				}
			], 
		  "date" : ISODate("2019-01-28T08:00:08.373Z")
		}
	
		Criteria criteria = Criteria.where("_id").is(id);
        criteria.and("groups").elemMatch(Criteria.where("group_id").is(group_id));

        Query query = new Query(criteria);
		
		Update update = new Update();
		if(data.get("name") != null){
			update.set("groups.$.name", "name");	//写法是这种的，就是定位到某个数组的成员，更新
			flag = true;
		}
		if(data.get("status") != null){
			update.set("groups.$.status", "status");	
			flag = true;
		}
		
		mongoTemplate.updateFirst(query, update, COLLECTION_NAME);

