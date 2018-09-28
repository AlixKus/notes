# REST Cilents
## 使用 RestTemplate
RestTemplate是一个同步的HTTP客户端,实现了单行调用REST端点。
> 从5.0开始，不阻塞 reactive 的`WebClient`会取代`RestTemplate`

## 初始化
默认构造方法使用`java.net.HttpURLConnection`,可以使用不同的实现了`ClientHttpRequestFactory`的HTTP库，内置了以下构造
- Apache HttpComponents
- Netty
- OkHttp

例如使用`Apache HttpComponents`
```
RestTemplate template = new RestTemplate(new HttpComponentsClientHttpRequestFactory());
```

## URI
许多URI支持模板变量,可以使用String变量或Map<String, String>来替换模板变量
例如
```
String result = restTemplate.getForObject(
        "http://example.com/hotels/{hotel}/bookings/{booking}", String.class, "42", "21");
```
或用Map
```
Map<String, String> vars = Collections.singletonMap("hotel", "42");
String result = restTemplate.getForObject(
        "http://example.com/hotels/{hotel}/rooms/{hotel}", String.class, vars);
```
URI会自动编码
```
restTemplate.getForObject("http://example.com/hotel list", String.class);
// Results in request to "http://example.com/hotel%20list"
```

## 获取json字符串
`getForObject`类型为`String.class`,会返回响应体。

一句话唐诗
```
RestTemplate cilent = new RestTemplate(new SimpleClientHttpRequestFactory());
String url = "http://api.apiopen.top/recommendPoetry";
String result = cilent.getForObject(url, String.class);
log.info(result);
```

## 自定义Headers
使用`exchange()`可以自定义`Headers`
```
String uriTemplate = "http://example.com/hotels/{hotel}";
URI uri = UriComponentsBuilder.fromUriString(uriTemplate).build(42);

RequestEntity<Void> requestEntity = RequestEntity.get(uri)
        .header(("MyRequestHeader", "MyValue")
        .build();

ResponseEntity<String> response = template.exchange(requestEntity, String.class);

String responseHeader = response.getHeaders().getFirst("MyResponseHeader");
String body = response.getBody();
```

## Body
在`HttpMessageConverter`帮助下，转换raw内容
`POST`一个对象
```
URI location = template.postForLocation("http://example.com/people", person);
```
`Content-Type`不需要显示设置。大多数情况下消息转换器会自动转换类型。如果必要可是使用`exchange`体统`Content-Type`,他会反过来影响使用何种转换器。
> JSON请求体
```
HttpHeaders headers = new HttpHeaders();
		headers.setContentType(MediaType.APPLICATION_JSON);
		HttpEntity<Map> entity = new HttpEntity<Map>(map, headers);
```


## Jackson JSON Views
可以指定`Jackson JSON`来序列化对象。
```
MappingJacksonValue value = new MappingJacksonValue(new User("eric", "7!jd#h23"));
value.setSerializationView(User.WithoutPasswordView.class);

RequestEntity<MappingJacksonValue> requestEntity =
    RequestEntity.post(new URI("http://example.com/user")).body(value);

ResponseEntity<String> response = template.exchange(requestEntity, String.class);
```
