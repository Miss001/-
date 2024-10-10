# navicate连接报错
navicate版本: 15
postgresql版本 : 17
pg_database.datlastsysoid does not exist anymore 
原因：postgresql17移除datlastsysoid，版本不兼容，升级navicate为16/17，或降低postgresql17版本
