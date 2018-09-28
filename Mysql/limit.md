select * from Customer LIMIT 10;--检索前10行数据，显示1-10条数据
select * from Customer LIMIT 1,10;--检索从第2行开始，累加10条id记录，共显示id为2....11
select * from Customer limit 5,10;--检索从第6行开始向前加10条数据，共显示id为6,7....15
select * from Customer limit 6,10;--检索从第7行开始向前加10条记录，显示id为7,8...16