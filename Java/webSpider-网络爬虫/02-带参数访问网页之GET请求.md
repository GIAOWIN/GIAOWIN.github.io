```java
    package com.yuan.webspider.leran;
    
    import org.apache.http.HttpEntity;
    import org.apache.http.client.methods.CloseableHttpResponse;
    import org.apache.http.client.methods.HttpGet;
    import org.apache.http.client.utils.URIBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClients;
    import org.apache.http.util.EntityUtils;
    
    import java.io.IOException;
    
    public class HttpGetDemo之带参数访问网页之GET请求 {
        public static void main(String[] args) throws Exception {
    
            //1.打开浏览器 创建Http对象
            CloseableHttpClient client = HttpClients.createDefault();
    
            //2.输入网址
            URIBuilder uriBuilder = new URIBuilder("https://www.bilibili.com/anime/timeline/");
            uriBuilder.addParameter("spm_id_from","333.12.b_7072696d6172795f6d656e75.13");
    
            HttpGet httpGet = new HttpGet(uriBuilder.build());
    
            //3.按回车 发起请求 返回响应
            CloseableHttpResponse response = client.execute(httpGet);
    
            //4.解析响应获取数据
            if (response.getStatusLine().getStatusCode() == 200){
                //响应码如果是200就获取响应体
                HttpEntity entity = response.getEntity();
    
    //            打印响应体
                String content = EntityUtils.toString(entity);
                System.out.println("content = " + content);
                System.out.println("httpGet = " + httpGet);
            }
        }
    }
```