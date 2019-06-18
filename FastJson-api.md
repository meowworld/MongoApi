
#FastJson 操作Api

#一.JSON 语法
	
	#1.JSON 语法规则

	数据在名称/值对中
	数据由逗号分隔
	大括号保存对象
	中括号保存数组

#Json 操作
	
	JSON.toJSONString()将对象转换成 JSON 字符串
	JSON.parseObject() 将 JSON 字符串转换成对象。
	
		Person person = new Person(20, "John", "Doe", new Date());
		String jsonObject = JSON.toJSONString(person);
		Person newPerson = JSON.parseObject(jsonObject, Person.class);
	
	#注意：FastJson 在进行操作时，是根据 getter 和 setter 的方法进行的，并不是依据 Field 进行。
	
	public @interface JSONField {
		// 配置序列化和反序列化的顺序，1.1.42版本之后才支持
		int ordinal() default 0;

		 // 指定字段的名称
		String name() default "";

		// 指定字段的格式，对日期格式有用
		String format() default "";

		// 是否序列化
		boolean serialize() default true;

		// 是否反序列化
		boolean deserialize() default true;
		
	}

	
	#FastJson 还支持 BeanToArray 序列化功能：
	#String jsonOutput= JSON.toJSONString(listOfPersons, SerializerFeature.BeanToArray);
	
	输出结果为：

	[
		[
			15,
			1469003271063,
			"John Doe"
		],
		[
			20,
			1469003271063,
			"Janette Doe"
		]
	]

#创建 JSON 对象

	JSONObject 和 JSONArray
	
	#JSONObject 
	
		#把JSONObject 当成一个 Map<String,Object>
		
		Map 有的方法他都有，还有一些自己的api
		
		JSONObject jsonObject = new JSONObject();

        jsonObject.put("name", "喵喵");
        jsonObject.put("age", 18);
        jsonObject.put("mick", "meow");

        log.info(jsonObject.toJSONString());
	
	#JSONArray	
	
		把 JSONArray 当做一个 List<Object>，可以把 JSONArray 看成 JSONObject 对象的一个集合。

		String jsonOutput= JSON.toJSONString(listOfPersons, SerializerFeature.BeanToArray);
	

//使用 ContextValueFilter 配置 JSON 转换


	Map map= JSON.parseObject(JSON.toJSONString(writeResult), new TypeReference<Map>(){});

	
