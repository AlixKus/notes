#添加远程用户
create user test identified by '123456';
grant all privileges on *.* to 'test'@'%'identified by '123456' with grant option;
flush privileges ;

#修改指定用户密码
update mysql.user set password=password('新密码') where User="test" and Host="localhost";

#删除用户
delete from user where User='test' and Host='localhost';

#查看用户
select Host,User,Password from mysql.user;