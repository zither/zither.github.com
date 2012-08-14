---
layout: post
title: 数据库迁移-Migrations
---

{{ page.title }}
================

<p class="meta">14 Aug 2012 - By MiniMee</p>

在[命令行工具Artisan](http://blog.minimee.org/2012/08/05/Artisan.html)中曾提到过Migrations，如果你熟悉Rails，那么对迁移肯定不会陌生。

Laravel框架为我们也提供了Migrate，打开laravel/cli/tasks/migrate/migrator.php文件我们可以看到Laravel提供的所有方法。

    install/make/migrate/rollback/reset

我们可以使用的大概就这五个：install初始化migrate，make用来创建任务，migrate执行任务，rollback回滚上一个任务，reset回滚所有任务。

前三个都非常直白，后两个是用来做回滚操作的。初始化migrate的时候会自动生成一张laravel_migrations表，其中有三个字段bundle、name和batch。

以[命令行工具Artisan](http://blog.minimee.org/2012/08/05/Artisan.html)文章写的为例，我们创建了一个users表，那么migrate就会在laravel_migrations表中查入一条数据，bundle为application，name是creat_users_table任务的名字，batch为任务批次。因为我们只用mirage创建过一张users表，因此此时laravel_migrations表中的batch为1。此后任务的batch会递增，相当于任务的序号。

现在我们可以执行命令：

    php artisan migrate:rollback

执行完之后我们会发现之前建立的users表被删除了，同时laravel_migrations表中关于users表的数据也删除了。你肯定已经猜到了mirage回滚的运行方式。

之前建立的users表之所会被删除，是因为回滚时执行了我们在任务的down()方法中填写的命令：

    Schema::drop('users');

需要我们注意到是，如果没有在down()方法中加上上面的命令，在回滚的时候migrate只会删除laravel_migrations表中的记录。users表依然会保存在数据库中，那当我们再次使用migrate方法时就会提示错误，因为users表已经存在了。

migrate的rollback只回滚最后一次任务，也就是laravel_migrations表中batch批次最大的任务，而reset操作则会回滚所有任务。

我们来拓展下，Laravel中的migrate在任务中为我们提供了up和down两种方法，其中up方法会在执行迁移时运行，而down方法则是在回滚时运行。在框架的文档中只告诉我们可以用它来创建表和删除表，其实我们可以用它来做更多的事情，比如修改表，插入记录等等。

我们用插入记录来举个例子，我们用artisan工具新建一个任务，取名为update_users_table:

    php artsian migrate:make update_users_table

我们为up方法和down方法分别添加对应的任务：

    public function up()
    {
         DB::table('users')->insert(array(
                            'username'=>'test',
                            'password'=>Hash::make('654321'),
                            'name'=>'test'
                        ));
    }
    public function down()
    {
         DB::table('users')->where('username','=','test')->delete();
    }

然后我们运行命令：

    php artisan migrate

打开数据库我们可以看到，migrate执行了我们在up方法中写的命令，往users表中插入了一行数据。那让我们回滚一下试试：

    php artisan migrate:rollback

不出意外的话，你会发现我们刚才插入数据不见了，因为执行了我们在down方法中填写的删除数据命令。

最后我们总结下，migrations任务中的up和down方法对应了migrate的执行和回滚操作，我们可以用它来执行大多数对数据库的操作命令，并且可以随时回滚，纠正错误。
