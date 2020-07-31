https://github.com/Miao4099/VertxApp/blob/master/images/framework.png
# 其它扩展功能
## 2.怎样添加一个自定义shop
Shop是对Vertx中Verticle的封装，可以让其使用标准的json配置。在程序中，可以创建一个或多个Agent shop接收不同端口的请求，然后将其打包为统一格式的json发给后方的Task Shop，task shop就是完成你的任务的shop，可以多个。具体框架参考下图
[![](https://github.com/Miao4099/VertxApp/blob/master/images/framework.png)](https://github.com/Miao4099/VertxApp/blob/master/images/framework.png "markdown")

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
