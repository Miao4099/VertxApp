
# 其它扩展功能
## 2.怎样添加一个自定义shop
Shop是对Vertx中Verticle的封装，可以让其使用标准的json配置。在程序中，可以创建一个或多个Agent shop接收不同端口的请求，然后将其打包为统一格式的json发给后方的Task Shop，task shop就是完成你的任务的shop，可以多个。具体框架参考下图  
        ![image](https://github.com/Miao4099/VertxApp/blob/master/images/framework.png)   
更详细的步骤可以参考 “快速上手” 部分

## 3.怎样读取输入json中的数据
        var sql = sqlUser.sql()
                .set<String>("user_id", msg, ValId())
                .set<String>("user_avatar", msg,ValImage("头像",true))
                .set<String>("user_name", msg, ValName())
                .set<String>("user_role", msg, ValRole())
                .set<String>("memo", msg, ValMemo())
                .set<Int>("user_sex", msg,ValSex(true))
                .set("user_password", IDGen.md5(password!!))
                .getUpdate("where user_id='${msg.jsonGet<String>("user_id")}'")

set里包装了从json中获取数据的各种方式。set<xxx>,xxx是指要读取的参数类型，如String,Int，Boolean等；“user_id”,"user_avatar"等是指数据表的字段名称，msg是标准的输入参数

## 4.直接操作一张数据表和字段名称映射

一般来说，前端和后端的使用的字段名称是不太一样的，有的是因为设计原因、有的是故意保密。无论何种原因，后端对做这种转换是深恶痛绝的，而且会被前端的代码所影响要反复修改，前端也是面临类似的问题。

        var sql = sqlUser.sql()
                .set<String>("user_id", msg, ValId())
                .set<String>("user_avatar", msg,ValImage("头像",true))
                .set<String>("user_name", msg, ValName())
                .set<String>("user_role", msg, ValRole())
                .set<String>("memo", msg, ValMemo())
                .set<Int>("user_sex", msg,ValSex(true))
                .set("user_password", IDGen.md5(password!!))
                .getUpdate("where user_id='${msg.jsonGet<String>("user_id")}'")

上面代码中使用的**sqlUser**是放置字段名称映射的工具类的实例，其中包含2个字段的映射关系：score：index_of_score, city:user_citry, index_of_score和user_city是sql表中的字段名称，用户可以添加类似的内容。users是数据表的名称。

        var sqlUser=SqlBuilder("users", mapOf(
                Pair("score","index_of_score"),
                Pair("city","user_city"))
        )
        
## 5.间接操作复杂数据的方式
上面的第4个问题，是操作一张简单的表。如果比较复杂的数据，比如是多个数据表join查出的数据，还需要进行分页、过滤 、排序处理，也可以简单的结合使用from接口，并使用标准的过滤器、排序器、分页器解析。

        fun AppMySql.msgMachineList(key: String, msg: JsonObject, message: Message<JsonObject>): Msg {
            return tryDo(message) {
                //获得标准的分页请求参数 
                var pi=msg.pageInfo()

                //解析json获取排序参数，输出结果默认按create_time字段进行降序排序
                var qs=sqlMachine.qs(msg, QuerySorter.Rule("create_time", "desc"))

                //from是将一个复杂的sql语句中直接作为一张表来处理
                var sql = sqlMachine.sql()
                        .from("SELECT H.*,K.NAME as shop_name,K.contact,K.phone AS contact_phone,K.slogan FROM (\n" +
                                "SELECT machine_number,`status`,remark,province,city,district,address,D.*FROM (\n" +
                                "SELECT A.machine_id,machine_number,province,city,district,address,B.`status`,B.remark FROM                                             machine_location A RIGHT JOIN machines B ON A.machine_id=B.id) C LEFT JOIN machine_setting D ON                                 C.machine_id=D.machine_id GROUP BY machine_number) H left JOIN store K ON                                                               K.machine_id=H.machine_id ")
                        .getList(sqlMachine.fs(msg), qs, pi)//解析json获取过滤参数后，与qs，pi一起输入生成产生list的sql语句

                //查询出列表
                listQuery(sql, message,{json->
                    //分析输出的结果，结果是json，里面包含数据的array    
                    var qr=QueryResult(json)
                    //每一行输出都做部分处理
                    qr.loopRows { addNewsField(it) }
                })
            }
        }


上面代码中使用的**sqlMachine**是放置字段名称映射的工具类的实例，用户还是可以添加字段映射的内容。

