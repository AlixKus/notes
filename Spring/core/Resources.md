#Why
`java.net.URL`不能处理好访问低层次的资源,比如不能访问classpath,servletContext，
Spring提供了`Resource`接口(类似于"http:"前缀)

#What
```
public interface Resource extends InputStreamSource {

    boolean exists();

    boolean isOpen();

    URL getURL() throws IOException;

    File getFile() throws IOException;

    Resource createRelative(String relativePath) throws IOException;

    String getFilename();

    String getDescription();

}
```
```
public interface InputStreamSource {

    InputStream getInputStream() throws IOException;

}
```

#对资源的抽象
一般使用其String形式
"classpath:.."
