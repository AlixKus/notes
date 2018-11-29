```
// 创建`FormDate`对象
formDate = new FormDate()
// 添加文件和其他参数
formDate.append("file",this,file)
formDate.append("name",this,name)
// 上传
axios.post(url,formDate).then(function(response){  
  })
```
