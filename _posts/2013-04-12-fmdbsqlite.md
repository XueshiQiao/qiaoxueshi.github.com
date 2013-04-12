---
layout: post
title: "简单封装FMDB操作sqlite的模板"
description: ""
category: iOS 
tags: [iOS, FMDB, sqlite]
---
{% include JB/setup %}

[FMDB](https://github.com/ccgus/fmdb)是Objective-C上操作Sqlite的开源库，与原生的操作sqlite数据库相比，有以下几个优点：

1. 操作方便、简单、代码优雅，易于维护;

2. 线程安全，用着更放心，很少出现过锁死数据库文件以及Crash的现象。

FMDatabase不是线程安全的，一个FMDatabase对象一定不能在多线程中使用，为了保证线程安全，可以在FMDB中采取下面两种方式：

1. 每个线程都创建一个FMDatabase对象，使用之前打开连接，用完关闭销毁；
2. 使用FMDatabaseQueue来保证线程安全，一个FMDatabaseQueue的对象可以在多线程中共享使用。

使用FMDatabase时，一般这样来做：

{% highlight c linenos %}
//创建一个 FMDatabase的对象
FMDatabase *db = [FMDatabase databaseWithPath:@"/tmp/tmp.db"];
//判断db是否打开，在使用之前一定要确保是打开的
if ([db open]) {
	//使用FMDatabase操作数据库 
	FMResultSet *s = [db executeQuery:@"SELECT * FROM myTable"];
	while ([s next]) {
    		//retrieve values for each record
	}
	
	//关闭
	[db close];
}
db = nil;
{% endhighlight %}

上面的这段代码是使用FMDatabase操作数据库的一个典型的使用方式，可以看到，其实我们关注的只是使用它来对数据库进行增删改查的操作，却每次都要写这些打开和关闭的操作，代码也显得臃肿，bad smell。用过Java中著名的Spring框架的同学都记得里面对数据库操作提供了一个Template的机制，比如JdbcTemplate、HibernateTemplate等，使用回调函数非常优雅的分离了创建连接、关闭连接和使用数据库连接操作数据库，下面就来模拟这个实现。
首先做个抽象，在上面代码的真正的逻辑中，我们只要拿到db变量就能满足我们的需要了，那么我们就把这一块抽象出来，在这里我们使用oc里的block来实现回调功能：

<pre><code>
//创建一个工具类TWFmdbUtil
@implementation TWFmdbUtil
+ (void) execSqlInFmdb:(void (^)(FMDatabase *db))block {
    NSString *dbPath = @"dbpath"; //sqlite数据库文件的路径
    //创建一个FMDatabase的对象
    FMDatabase *db = [FMDatabase databaseWithPath:dbPath];
    //使用之前保证数据库是打开的
    if ([db open]) {
        @try {
            block(db); //调用block来回调实现具体的逻辑
        }
        @catch (NSException *exception) {
            //处理异常，也可以直接抛出，这样调用者就能捕获到异常信息
            NSLog(@"TWFmdbUtil exec sql exception: %@", exception);
        }
        @finally {
            [db close]; //如果[db open]就要保证能关闭
        }
    } else { 
        //如果打开失败，则打印出错误信息
        NSLog(@"db open failed, path:%@, errorMsg:%@", dbPath, [db lastError]);
    }
    db = nil;
}
@end
</code></pre>

现在使用的时候就能够像下面这样来实现了：

<pre><code>
[TWFmdbUtil execSqlInFmdb:^(FMDatabase *db) {
	//处理业务逻辑
	FMResultSet *s = [db executeQuery:@"SELECT * FROM myTable"];
	while ([s next]) {
    		//retrieve values for each record
	}
}];
</code></pre>

这样的代码看起来是不是优雅多了呢？我们无需关心数据库的创建和关闭操作，只需要关心我们的业务逻辑就可以了。

历史总是惊人的相似，FMDatabaseQueue的使用就是采用这样的方式来处理的，来看一段fmdb主页上提供的一个例子：

<pre><code>
FMDatabaseQueue *queue = [FMDatabaseQueue databaseQueueWithPath:aPath];
[queue inDatabase:^(FMDatabase *db) {
    [db executeUpdate:@"INSERT INTO myTable VALUES (?)", [NSNumber numberWithInt:1]];
    [db executeUpdate:@"INSERT INTO myTable VALUES (?)", [NSNumber numberWithInt:2]];
    [db executeUpdate:@"INSERT INTO myTable VALUES (?)", [NSNumber numberWithInt:3]];

    FMResultSet *rs = [db executeQuery:@"select * from foo"];
    while ([rs next]) {
        …
    }
}];
</code></pre>

更多实例请移步FMDB在GitHub上的[主页](https://github.com/ccgus/fmdb)
或者访问@唐巧_boy 关于FMDB的[这篇文章](http://blog.devtang.com/blog/2012/04/22/use-fmdb/)

Have Fun！

