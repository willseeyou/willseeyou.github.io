---
layout: post
title: "Oracle: ORA-28001: The password has expired"
date: 2016-02-15 09:12:00 +0800
categories: oracle
---
### ORA-28001: The password has expired
![Oracle ORA-28001](/images/oracle-error/ora-28001_error.png)

### Solution
* Connect as sysdba:

~~~
sqlplus / as sysdba
~~~

* Set password's life time to unlimited:

~~~
ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED;
~~~

* Set new password for the locked user:

~~~
ALTER USER user_name IDENTIFIED BY password;
~~~

* Unlock user:

~~~
ALTER USER user_name ACCOUNT UNLOCK;
~~~

* Make sure user not locked any more:

~~~
SELECT USERNAME, ACCOUNT_STATUS FROM DBA_USERS;
~~~

[Reference: ORA-28001: The password has expired](https://hecpv.wordpress.com/2014/10/16/how-to-solve-ora-28001-the-password-has-expired/)<br>
[Reference: Stackoverflow](http://stackoverflow.com/questions/16870466/change-password-in-sql-developer-after-ora-28001-the-password-has-expired)
