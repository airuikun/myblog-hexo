
---
title: Mongodb For Windows
date: 2015-12-12 16:30:35
description: "在开源中国看到的，每一篇都是精华"
tags: [分享, javascript]
image:
  background: witewall_3.png
comments: true
share: true
---
### 安装mongodb

1、 **官网下载** [mongodb][1]，如果嫌慢还可以前往[百度云盘][2]
2、 如果是**安装**包，那么安装方式选 <kbd>Custom</kbd> 这样可以自定义安装，比如将路径设置在 <code>F:\MongoDb\</code> 如果是 _压缩包_ 就不说什么了
3、 **创建日志** 

<!--more-->

4、 **运行命令行** （CMD、Bash） 都行，以Bash为例：

    $ cd /f/MongoDb/bin/
    $ mongod -dbpath "/f/MongoDb/data/db"

    2014-12-31T11:10:31.408+0800 [initandlisten] MongoDB starting : pid=5528 port=27
    017 dbpath=f:/MongoDb/data/db 64-bit host=highsea-PC
    2014-12-31T11:10:31.408+0800 [initandlisten] targetMinOS: Windows 7/Windows Serv
    er 2008 R2
    2014-12-31T11:10:31.408+0800 [initandlisten] db version v2.6.6
    2014-12-31T11:10:31.408+0800 [initandlisten] git version: 608e8bc319627693b04cc7
    da29ecc300a5f45a1f……以下省略

可以看到 mongodb 已经启动，端口号：27017

5、 **测试连接** 新开一个Bash窗口 

    $ cd /f/MongoDb/bin/&&mongo
    MongoDB shell version: 2.6.6
    connecting to: test
    Welcome to the MongoDB shell.
    For interactive help, type "help".
    For more comprehensive documentation, see
        http://docs.mongodb.org/
    Questions? Try the support group
        http://groups.google.com/group/mongodb-user
    >_

&&符号是同时执行多个指令；退出测试请输入 <kbd>Ctrl+c</kbd>

    > ^C
    bye

6、 **创建 Windows 服务** 

    $ mongod --dbpath "/f/MongoDb/data/db" --logpath "/f/MongoDb/data/log/MongDb.log" --install --serviceName MongoDb

这时候进入log目录发现 MongDb.log 里面写了：

    2014-12-31T11:45:02.232+0800 Trying to install Windows service 'MongoDb'
    2014-12-31T11:45:03.077+0800 Service 'MongoDb' (MongoDB) installed with command line 'f:\MongoDb\bin\mongod.exe --dbpath f:/MongoDb/data/db --logpath f:/MongoDb/data/log/MongDb.log --service'
    2014-12-31T11:45:03.077+0800 Service can be started from the command line with 'net start MongoDb'

输入指令：

    $ net start MongoDb
    MongoDb 服务正在启动 .
    MongoDb 服务已经启动成功。

到此，windows Mongodb 服务启动成功；试试在浏览器中输入 [localhost:27017][3] ，会显示什么？

7、 **关闭服务** 与 **删除服务** ，指令：

> + 关闭服务

    $ net stop MongoDB
    发生系统错误 109。
    管道已结束。

此时有提示： **发生系统错误 109** 不要紧，只要进入 <code> \data\db\ </code> 目录删除 mongodb.lok 文件重新启动服务即可（不行？多试几次）。

> + 删除服务

    $ mongod --dbpath "/f/MongoDb/data/db" --logpath "/f/MongoDb/data/log/MongDb.log" --remove --serviceName "MongoDB"
    2014-12-31T12:50:44.304+0800 log file "f:/MongoDb/data/log/MongDb.log" exists; m
    oved to "f:/MongoDb/data/log/MongDb.log.2014-12-31T04-50-44".

此时进入 <code>MongDb.log</code> 发现文本为：

    2014-12-31T12:50:44.319+0800 Trying to remove Windows service 'MongoDB'
    2014-12-31T12:50:44.319+0800 Service 'MongoDB' removed

到此，mongodb 服务删除成功。


### mongodb 常用指令

1、 **启动 mongodb** ；方法不再赘述， 以 <code> $ net start MongoDB </code> 为例；

    $ cd /f/MongoDb/bin/&&net start MongoDb&&mongo

    MongoDb 服务已经启动成功。

    MongoDB shell version: 2.6.6
    connecting to: test
    > show dbs
    admin  (empty)
    local  0.078GB
    >_

2、 **增删改查** ，以及常见指令

    > db.highsea.save({"_id":"2","nickname":"high sea","job":"web前端开发工程师"})
    WriteResult({ "nInserted" : 1 })
    >_

> 注 此处如提示

     chunk 4114 failed with errno:1455 页面文件太小，无法完成操作。
    ……

则是服务器内存不够用了，建议增加虚拟内存或者物理内存

> 注 mongodb 中把关系数据库的"表"称作"集合" ……

以上创建了 名为”highsea“的表，（如果已存在 “highsea”的表 mongodb 会合并）并且插入了俩个字段 "nickname" 和 "job" ，主键是 “_id”，继续：

    > db.highsea.insert({"_id":"1","nickname":"Gao Hai","QQ":"644494365"})
    WriteResult({ "nInserted" : 1 })
    > db.highsea.insert({"_id":"3","email":"admin@highsea90.com"})
    WriteResult({ "nInserted" : 1 })
    > db.highsea.insert({"_id":"3","email":"644494365@qq.com"})
    WriteResult({
            "nInserted" : 0,
            "writeError" : {
                    "code" : 11000,
                    "errmsg" : "insertDocument :: caused by :: 11000 E11000 duplicat
    e key error index: highsea.highsea.$_id_  dup key: { : \"3\" }"
            }
    })

以上，我又插入一条 主键 "_id" 为 "1" 的数据，然后又重复插入了 "_id"为"2" 的数据，mongodb 报错，那换成 <code>save</code> 继续：

    > db.highsea.save({"_id":"3","email":"644494365@qq.com"})
    WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })

mongodb 提示 Modified 成功。（这也是 save 和 insert 的区别）
此时 

    > db.highsea.find()
    { "_id" : "1", "nickname" : "Gao Hai", "QQ" : "644494365" }
    { "_id" : "2", "nickname" : "high sea", "job" : "web前端开发工程师" }
    { "_id" : "3", "email" : "644494365@qq.com" }

    > db.highsea.find({"QQ":"644494365"})
    { "_id" : "1", "nickname" : "Gao Hai", "QQ" : "644494365" }

    > show dbs
    admin    (empty)
    highsea  0.078GB
    local    0.078GB
    test     0.078GB

    > db.version()
    2.6.6

    > db.getMongo()
    connection to 127.0.0.1

    > db.addUser("hs","mg123","true")
    WARNING: The 'addUser' shell helper is DEPRECATED. Please use 'createUser' inste
    ad
    Successfully added user: { "user" : "hs", "roles" : [ "read" ] }

    > show users
    {
            "_id" : "highsea.hs",
            "user" : "hs",
            "db" : "highsea",
            "roles" : [
                    {
                            "role" : "read",
                            "db" : "highsea"
                    }
            ]
    }

    > show collections
    highsea
    system.indexes

    > db.createCollection("wellcome")
    { "ok" : 1 }

    > use highsea
    switched to db highsea

    > db.dropDatabase()
    { "dropped" : "highsea", "ok" : 1 }

    > show dbs
    admin     0.078GB
    local     0.078GB
    test      0.078GB
    wellcome  (empty)

    >_

这里有个提示： 2.6.6 版本已经不推荐使用 addUser 了，请使用 createUser


> + 注：输入中文时注意光标


以上展示了 指令 ：

    show dbs                    显示所有数据库
    db.foo.save({})             创建|更新某个数据库
    db.foo.insert({})           插入某个数据库
    db.foo.find()               显示某个数据库
    db.foo.find({"foo":"foo"})  对于当前数据库中的foo集合进行查找，条件是数据中有一个属性叫foo，且foo的值为foo
    db.version()                查看当前版本
    db.getMongo()               获取链接当前数据库的服务器
    db.addUser()                增加数据库用户
    show users                  查看用户
    show collections            显示当前数据库的集合
    db.createCollection("foo")  创建一个名为"foo"的空数据库
    use foo                     切换到某个数据库
    db.dropDatabase()           删除当前数据库

其他指令 可以使用 <code> help | db.help() | db.yourColl.help() |  db.youColl.find().help() </code>

附： Mongodb 数据库操作 help 指令 中文版

> + help

        db.help()                    help on db methods
        db.mycoll.help()             help on collection methods
        sh.help()                    sharding helpers
        rs.help()                    replica set helpers
        help admin                   administrative help
        help connect                 connecting to a db help
        help keys                    key shortcuts
        help misc                    misc things to know
        help mr                      mapreduce
        show dbs                     show database names
        show collections             show collections in current database
        show users                   show users in current database
        show profile                 show most recent system.profile entries with time >= 1ms
        show logs                    show the accessible logger names
        show log [name]              prints out the last segment of log in memory, 'global' is default
        use <db_name>                set current database
        db.foo.find()                list objects in collection foo
        db.foo.find( { a : 1 } )     list objects in foo where a == 1
        it                           result of the last line evaluated; use to further iterate
        DBQuery.shellBatchSize = x   set default number of items to display on shell
        exit                         quit the mongo shell


>  db.help();

    DB methods:
        db.adminCommand(nameOrDocument) - switches to 'admin' db, and runs command [ just calls db.runCommand(...) ]
        db.auth(username, password)
        db.cloneDatabase(fromhost)
        db.commandHelp(name) returns the help for the command
        db.copyDatabase(fromdb, todb, fromhost)
        db.createCollection(name, { size : ..., capped : ..., max : ... } )
        db.createUser(userDocument)
        db.currentOp() displays currently executing operations in the db
        db.dropDatabase()
        db.eval(func, args) run code server-side
        db.fsyncLock() flush data to disk and lock server for backups
        db.fsyncUnlock() unlocks server following a db.fsyncLock()
        db.getCollection(cname) same as db['cname'] or db.cname
        db.getCollectionNames()
        db.getLastError() - just returns the err msg string
        db.getLastErrorObj() - return full status object
        db.getMongo() get the server connection object
        db.getMongo().setSlaveOk() allow queries on a replication slave server
        db.getName()
        db.getPrevError()
        db.getProfilingLevel() - deprecated
        db.getProfilingStatus() - returns if profiling is on and slow threshold
        db.getReplicationInfo()
        db.getSiblingDB(name) get the db at the same server as this one
        db.getWriteConcern() - returns the write concern used for any operations on this db, inherited from server object if set
        db.hostInfo() get details about the server's host
        db.isMaster() check replica primary status
        db.killOp(opid) kills the current operation in the db
        db.listCommands() lists all the db commands
        db.loadServerScripts() loads all the scripts in db.system.js
        db.logout()
        db.printCollectionStats()
        db.printReplicationInfo()
        db.printShardingStatus()
        db.printSlaveReplicationInfo()
        db.dropUser(username)
        db.repairDatabase()
        db.resetError()
        db.runCommand(cmdObj) run a database command.  if cmdObj is a string, turns it into { cmdObj : 1 }
        db.serverStatus()
        db.setProfilingLevel(level,<slowms>) 0=off 1=slow 2=all
        db.setWriteConcern( <write concern doc> ) - sets the write concern for writes to the db
        db.unsetWriteConcern( <write concern doc> ) - unsets the write concern for writes to the db
        db.setVerboseShell(flag) display extra information in shell output
        db.shutdownServer()
        db.stats()
        db.version() current version of the server

> +  db.yourColl.help();

    DBCollection help
        db.yourColl.find().help() - show DBCursor help
        db.yourColl.count()
        db.yourColl.copyTo(newColl) - duplicates collection by copying all documents to newColl; no indexes are copied.
        db.yourColl.convertToCapped(maxBytes) - calls {convertToCapped:'yourColl', size:maxBytes}} command
        db.yourColl.dataSize()
        db.yourColl.distinct( key ) - e.g. db.yourColl.distinct( 'x' )
        db.yourColl.drop() drop the collection
        db.yourColl.dropIndex(index) - e.g. db.yourColl.dropIndex( "indexName" ) or db.yourColl.dropIndex( { "indexKey" : 1 } )
        db.yourColl.dropIndexes()
        db.yourColl.ensureIndex(keypattern[,options]) - options is an object with these possible fields: name, unique, dropDups
        db.yourColl.reIndex()
        db.yourColl.find([query],[fields]) - query is an optional query filter. fields is optional set of fields to return. - e.g. db.yourColl.find( {x:77} , {name:1, x:1} )
        db.yourColl.find(...).count()
        db.yourColl.find(...).limit(n)
        db.yourColl.find(...).skip(n)
        db.yourColl.find(...).sort(...)
        db.yourColl.findOne([query])
        db.yourColl.findAndModify( { update : ... , remove : bool [, query: {}, sort: {}, 'new': false] } )
        db.yourColl.getDB() get DB object associated with collection
        db.yourColl.getPlanCache() get query plan cache associated with collection
        db.yourColl.getIndexes()
        db.yourColl.group( { key : ..., initial: ..., reduce : ...[, cond: ...]} )
        db.yourColl.insert(obj)
        db.yourColl.mapReduce( mapFunction , reduceFunction , <optional params>)
        db.yourColl.aggregate( [pipeline], <optional params> ) - performs an aggregation on a collection; returns a cursor
        db.yourColl.remove(query)
        db.yourColl.renameCollection( newName , <dropTarget> ) renames the collection.
        db.yourColl.runCommand( name , <options> ) runs a db command with the given name where the first param is the collection name
        db.yourColl.save(obj)
        db.yourColl.stats()
        db.yourColl.storageSize() - includes free space allocated to this collection
        db.yourColl.totalIndexSize() - size in bytes of all the indexes
        db.yourColl.totalSize() - storage allocated for all data and indexes
        db.yourColl.update(query, object[, upsert_bool, multi_bool]) - instead of two flags, you can pass an object with fields: upsert, multi
        db.yourColl.validate( <full> ) - SLOW
        db.yourColl.getShardVersion() - only for use with sharding
        db.yourColl.getShardDistribution() - prints statistics about data distribution in the cluster
        db.yourColl.getSplitKeysForChunks( <maxChunkSize> ) - calculates split points over all chunks and returns splitter function
        db.yourColl.getWriteConcern() - returns the write concern used for any operations on this collection, inherited from server/db if set
        db.yourColl.setWriteConcern( <write concern doc> ) - sets the write concern for writes to the collection
        db.yourColl.unsetWriteConcern( <write concern doc> ) - unsets the writeconcern for writes to the collection

> + db.youColl.find().help();

    find() modifiers
        .sort( {...} )
        .limit( n )
        .skip( n )
        .count(applySkipLimit) - total # of objects matching query. by default ignores skip,limit
        .size() - total # of objects cursor would return, honors skip,limit
        .explain([verbose])
        .hint(...)
        .addOption(n) - adds op_query options -- see wire protocol
        ._addSpecial(name, value) - http://dochub.mongodb.org/core/advancedqueries#AdvancedQueries-Metaqueryoperators
        .batchSize(n) - sets the number of docs to return per getMore
        .showDiskLoc() - adds a $diskLoc field to each returned object
        .min(idxDoc)
        .max(idxDoc)
        .comment(comment)
        .snapshot()
        .readPref(mode, tagset)
        
    Cursor methods
        .toArray() - iterates through docs and returns an array of the results
        .forEach( func )
        .map( func )
        .hasNext()
        .next()
        .objsLeftInBatch() - returns count of docs left in current batch (when exhausted, a new getMore will be issued)
        .itcount() - iterates through documents and counts them
        .pretty() - pretty print each document, possibly over multiple lines



附：Mongodb help 指令 中文版
===============

    Options:

    General options:
      -h [ --help ]               show this usage information
      --version                   show version information
      -f [ --config ] arg         configuration file specifying additional options
      -v [ --verbose ] [=arg(=v)] be more verbose (include multiple times for more
                                  verbosity e.g. -vvvvv)
      --quiet                     quieter output
      --port arg                  specify port number - 27017 by default
      --bind_ip arg               comma separated list of ip addresses to listen on
                                  - all local ips by default
      --maxConns arg              max number of simultaneous connections - 1000000
                                  by default
      --logpath arg               log file to send write to instead of stdout - has
                                  to be a file, not directory
      --logappend                 append to logpath instead of over-writing
      --timeStampFormat arg       Desired format for timestamps in log messages.
                                  One of ctime, iso8601-utc or iso8601-local
      --pidfilepath arg           full path to pidfile (if not set, no pidfile is
                                  created)
      --keyFile arg               private key for cluster authentication
      --setParameter arg          Set a configurable parameter
      --httpinterface             enable http interface
      --clusterAuthMode arg       Authentication mode used for cluster
                                  authentication. Alternatives are
                                  (keyFile|sendKeyFile|sendX509|x509)
      --auth                      run with security
      --noauth                    run without security
      --ipv6                      enable IPv6 support (disabled by default)
      --jsonp                     allow JSONP access via http (has security
                                  implications)
      --rest                      turn on simple rest api
      --slowms arg (=100)         value of slow for profile and console log
      --profile arg               0=off 1=slow, 2=all
      --cpu                       periodically show cpu and iowait utilization
      --sysinfo                   print some diagnostic system information
      --dbpath arg                directory for datafiles - defaults to \data\db\
      --directoryperdb            each database will be stored in a separate
                                  directory
      --noIndexBuildRetry         don't retry any index builds that were
                                  interrupted by shutdown
      --noprealloc                disable data file preallocation - will often hurt
                                  performance
      --nssize arg (=16)          .ns file size (in MB) for new databases
      --quota                     limits each database to a certain number of files
                                  (8 default)
      --quotaFiles arg            number of files allowed per db, implies --quota
      --smallfiles                use a smaller default file size
      --syncdelay arg (=60)       seconds between disk syncs (0=never, but not
                                  recommended)
      --upgrade                   upgrade db if needed
      --repair                    run repair on all dbs
      --repairpath arg            root directory for repair files - defaults to
                                  dbpath
      --noscripting               disable scripting engine
      --notablescan               do not allow table scans
      --journal                   enable journaling
      --nojournal                 disable journaling (journaling is on by default
                                  for 64 bit)
      --journalOptions arg        journal diagnostic options
      --journalCommitInterval arg how often to group/batch commit (ms)

    Windows Service Control Manager options:
      --install                install Windows service
      --remove                 remove Windows service
      --reinstall              reinstall Windows service (equivalent to --remove
                               followed by --install)
      --serviceName arg        Windows service name
      --serviceDisplayName arg Windows service display name
      --serviceDescription arg Windows service description
      --serviceUser arg        account for service execution
      --servicePassword arg    password used to authenticate serviceUser

    Replication options:
      --oplogSize arg       size to use (in MB) for replication op log. default is
                            5% of disk space (i.e. large is good)

    Master/slave options (old; use replica sets instead):
      --master              master mode
      --slave               slave mode
      --source arg          when slave: specify master as <server:port>
      --only arg            when slave: specify a single database to replicate
      --slavedelay arg      specify delay (in seconds) to be used when applying
                            master ops to slave
      --autoresync          automatically resync if slave data is stale

    Replica set options:
      --replSet arg           arg is <setname>[/<optionalseedhostlist>]
      --replIndexPrefetch arg specify index prefetching behavior (if secondary)
                              [none|_id_only|all]

    Sharding options:
      --configsvr           declare this is a config db of a cluster; default port
                            27019; default dir /data/configdb
      --shardsvr            declare this is a shard db of a cluster; default port
                            27018





[1]: http://www.mongodb.org/downloads           "mongodb download"
[2]: http://pan.baidu.com/s/1EUCMi              "mongodb 百度云盘"
[3]: http://localhost:27017/                    "端口 27017"