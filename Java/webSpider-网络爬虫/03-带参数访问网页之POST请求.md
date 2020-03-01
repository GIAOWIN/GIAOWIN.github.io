```java
package com.yuan.webspider.leran;

import org.apache.http.HttpEntity;
import org.apache.http.NameValuePair;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.message.BasicNameValuePair;
import org.apache.http.util.EntityUtils;

import java.io.IOException;
import java.lang.reflect.Array;
import java.util.ArrayList;
import java.util.List;

public class HttpPostDemo之带参数访问网页之POST请求 {
    public static void main(String[] args) throws Exception {

        //1.打开浏览器 创建Http对象
        CloseableHttpClient client = HttpClients.createDefault();

        //2.输入网址
            //这里有变化POST【当然 应为访问方法是POST  所以理所当然的访问失败了】
        HttpPost httpPot = new HttpPost("https://www.bilibili.com/anime/timeline/");
            //声明List集合 封装表单参数
        List<NameValuePair> postParameter = new ArrayList<NameValuePair>();
        postParameter.add(new BasicNameValuePair("spm_id_from", "333.12.b_7072696d6172795f6d656e75.13"));
            //创建提交对象
        UrlEncodedFormEntity postEntity = new UrlEncodedFormEntity(postParameter, "utf-8");
            //将提交对象添加到post请求中
        httpPot.setEntity(postEntity);
            //3.按回车 发起请求 返回响应
        CloseableHttpResponse response = client.execute(httpPot);

        //4.解析响应获取数据
        if (response.getStatusLine().getStatusCode() == 200) {
            //响应码如果是200就获取响应体
            HttpEntity entity = response.getEntity();

//            打印响应体
            String content = EntityUtils.toString(entity);
            System.out.println("content = " + content);
            System.out.println("httpPot = " + httpPot);
        }
    }
}
```