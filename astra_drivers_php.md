---
layout: default
title: Astra + PHP
description: How to leverage Astra with Php
permalink: /astra/drivers/php/
---

# How To: PHP with Astra

One of the reasons I created my blog here was to be able to quickly create public content for tasks that I have in various stages of completion.  Normally this kind of information I create would stay in cryptic files that most people would not be able to understand.  Yesterday [Legacy Driver](https://docs.astra.datastax.com/docs/connecting-with-legacy-drivers) section was added to [Astra Docs](https://docs.astra.datastax.com/docs/) including the [PHP Driver](https://docs.astra.datastax.com/docs/php-legacy-drivers).  Now in a successful fashion I am able to quickly go back and get some PHP Driver work I had created early on in my journey here at [DataStax](https://datastax.com/).  Be sure to get comfortable with the Prerequisites and Procedures before continuing.

&nbsp; <b><i>Fun Fact: PHP is the 3rd programming language I learned and I have used PHP since the 90s.</i></b>  

# Why PHP and Astra?

Well, because we all still have legacy apps and now you want to move them to cassandra.  Astra is the perfect place to work on that migration and you can do it for free on the free tier. Since PHP is used in tons of websites and usually coupled with mysql under the hood this is a great data modeling and astra experience.  I wont get into data modeling too much here, but reach out if you want to have that discussion.  I do not work with PHP anymore, but for the purpose of this blog, I am going to expose some work I have done to hook PHP to Astra.

You can leverage Astra with PHP in three ways:

1.	Convert the PHP insert/update/delete/select to curl.
2.	Minimally convert code and implement the driver.
3.	Stop using php server side and use rest api with js/jquery. <b><i>#winning</i></b> 

# What Environment?

Node History for Php 7.1 w/ Cassandra on a Centos 7.4 Vagrant VM:

```js
  101  wget -q http://rpms.remirepo.net/enterprise/remi-release-7.rpm
  102  wget -q https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
  103  rpm -i remi-release-7.rpm epel-release-latest-7.noarch.rpm
  106  yum install yum-utils
  107  yum-config-manager --enable remi-php71
  108  yum install php php-devel php-pear
  109  yum install automake cmake gcc-c++ git libtool pcre-devel 
  111  yum install gmp gmp-devel

  113  wget http://downloads.datastax.com/cpp-driver/centos/7/dependencies/libuv/v1.8.0/libuv-1.8.0-1.el7.centos.x86_64.rpm
  114  wget http://downloads.datastax.com/cpp-driver/centos/7/dependencies/libuv/v1.8.0/libuv-devel-1.8.0-1.el7.centos.x86_64.rpm
  115  rpm -ivh libuv-1.8.0-1.el7.centos.x86_64.rpm
  116  rpm -ivh libuv-devel-1.8.0-1.el7.centos.x86_64.rpm
  117  yum install libuv libuv-devel
  143  wget https://downloads.datastax.com/cpp-driver/centos/7/cassandra/v2.7.0/cassandra-cpp-driver-2.7.0-1.el7.centos.x86_64.rpm
  144  wget https://downloads.datastax.com/cpp-driver/centos/7/cassandra/v2.7.0/cassandra-cpp-driver-devel-2.7.0-1.el7.centos.x86_64.rpm
  149  rpm -ivh cassandra-cpp-driver-2.7.0-1.el7.centos.x86_64.rpm 
  150  rpm -ivh cassandra-cpp-driver-devel-2.7.0-1.el7.centos.x86_64.rpm 
  151  pecl install cassandra
  160  php -r "echo php_ini_loaded_file();"
  161  nano /etc/php.ini 
```

With the above environment working for cassandra driver and php I can execute our test script against astra:

```js
<?php 

$ssl = Cassandra::ssl()
	->withTrustedCerts('/vagrant/ca.crt')
	->withClientCert('/vagrant/cert')
	->withPrivateKey('/vagrant/key', 'passphrase')
	->build();
$cluster = Cassandra::cluster()
	->withSSL($ssl)
	->withCredentials("demo", "demo2020")
	->withContactPoints('[databaseid]-us-east1.db.astra.datastax.com')
	->withPort(31025)
	->build();
$session = $cluster->connect();
$result = $session->execute('SELECT * FROM demo');
foreach ($result as $row) {
    printf("The result is \"%s\".\n", $row['result']);
}
 
?>
```

# What Code Changes?

Now that the environment and demo works, lets look at some code changes i had to make in the application:

BEFORE:
```js
function register($_INPUT) {

if(
	isset($_INPUT['registerSubmit']) && 
	$_INPUT['password'] == $_INPUT['confirm']
) {
	$query = "INSERT INTO `users` (`sessionid`,`usertype`,`password`,`email`,`ip`) VALUES('".session_id()."', 'member', PASSWORD('".$_POST['password']."'), '".$_POST['email']."', '".$_SERVER['REMOTE_ADDR']."');";
	$result = mysql_query($query) or mail('me',"register query fail",$query);
	$insert_id = mysql_insert_id();
	$_SESSION['userid'] = $insert_id;	
}
```

AFTER:
```js
function register($_INPUT) {
	if(
	   	isset($_INPUT['registerSubmit']) &&
	    $_INPUT['password'] == $_INPUT['confirm']
	) {
	   	$query = "INSERT INTO demo.users (id,sessionid,usertype,password,email,ip) VALUES(uuid(), '".session_id()."', 'member', '".$_POST['password']."', '".$_POST['email']."', '".$_SERVER['REMOTE_ADDR']."');";

	// astra cassandra php settings
	$ssl = Cassandra::ssl()
		->withTrustedCerts('/vagrant/ca.crt')
		->withClientCert('/vagrant/cert')
		->withPrivateKey('/vagrant/key', 'passphrase')
	   	->build();
	$cluster = Cassandra::cluster()
		->withSSL($ssl)
		->withCredentials("demo", "demo2020")
		->withContactPoints('[databaseid]-us-east1.db.astra.datastax.com')
		->withPort(31025)
		->build$
	$options = new Cassandra\ExecutionOptions(array('consistency' => Cassandra::CONSISTENCY_ALL));
	$session = $cluster->connect();
	$result = $session->execute($query,$options);
}
```

Notice anything different other than inherit differences for the cassandra driver?  Yup, no mysql_insert_id() here and uuid() function in the insert.  When migrating a legacy app to cassandra, not only do you have to think outside of the box for data modeling, you also have to be mindful in the application layer.  To solve this problem, I completely removed the dependency on a user id in the application.  We dont need no stinking incremental ids in cassandra!!

# What's Next?

Are you still using PHP?  Reach out, I can show you how to modernize your application experience with futureproof modern and serverless applications.  If you are unable to modernize your app completely and you want to migrate to Astra or Cassandra using this Legacy PHP Driver,  I can help out with that too.  


{% include astra_help.html %}