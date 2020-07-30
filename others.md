
# 其它扩展功能
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
