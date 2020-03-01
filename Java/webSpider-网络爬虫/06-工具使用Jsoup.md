```java
package com.yuan.webspider.leran;

import org.apache.commons.io.FileUtils;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Attribute;
import org.jsoup.nodes.Attributes;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;
import org.junit.Test;

import java.io.File;
import java.io.IOException;
import java.net.URL;
import java.util.Set;

public class 工具之使用Jsoup {
    /**
    * Jsoup解析URL
    *
    * @throws IOException
    */
    @Test
    public void urlTest() throws IOException {
        //1.解析url地址
        //参数 url,连接超时时间
        Document document = Jsoup.parse(new URL("http://www.baidu.com"), 10000);

        //使用标签选择器，获取title标签中的内容
        String title = document.getElementsByTag("title").first().text();

        System.out.println("title = " + title);
    }

    /**
    * Jsoup解析字符串
    *
    * @throws IOException
    */
    @Test
    public void stringTest() throws IOException {

        //读取文件，获取字符串
        String file = FileUtils.readFileToString(new File("C:\\Users\\ASUS\\Desktop\\index.html"), "utf8");
        System.out.println("file = " + file);
        //解析字符串
        Document document = Jsoup.parse(file);
        String title = document.getElementsByTag("title").first().text();

        System.out.println("title = " + title);
    }


    /**
    * Jsoup解析文件
    *
    * @throws IOException
    */
    @Test
    public void fileTest() throws IOException {

        //读取文件，获取字符串
        String file = FileUtils.readFileToString(new File("C:\\Users\\ASUS\\Desktop\\index.html"), "utf8");
        System.out.println("file = " + file);
        //解析字符串
        Document document = Jsoup.parse(file);
        String title = document.getElementsByTag("title").first().text();

        System.out.println("title = " + title);
    }

    /**
    * 使用DOM的方式获取元素
    *
    * @throws IOException
    */
    @Test
    public void domTest() throws IOException {

        //解析文件获取Dom对象
        Document document = Jsoup.parse(new File("C:\\Users\\ASUS\\Desktop\\index.html"), "utf8");

        System.out.println("--------------------ID----------------------");
        //根据ID获取元素以及元素内容【getElementById】
        Element docsID = document.getElementById("loading-mask");
        System.out.println("docsID = " + docsID);

        System.out.println("--------------------标签----------------------");
        //根据标签来获取【getElementsByTag】
        Element div = document.getElementsByTag("div").first();
        System.out.println("div = " + div);

        System.out.println("--------------------class----------------------");
        //根据class来获取【getElementsByClass】
        //class标签如果引入了两个class 那么 有其中一个就会定位到想要的元素
        Elements aClass = document.getElementsByClass("loading-indicator");
        System.out.println("aClass = " + aClass.text());

        System.out.println("--------------------属性----------------------");
        //根据属性来获取元素【getElementsByClass】
        //class标签如果引入了两个class 那么 有其中一个就会定位到想要的元素
        Elements attribute = document.getElementsByAttribute("like");
        System.out.println("attribute = " + attribute.text());

    }

    /**
    * 获取元素中的数据
    *
    * @throws IOException
    */
    @Test
    public void dataTest() throws IOException {

        //解析文件获取Dom对象
        Document document = Jsoup.parse(new File("C:\\Users\\ASUS\\Desktop\\index.html"), "utf8");
        System.out.println("--------------根据id获取元素--------------");
        //根据id获取元素【getElementById】
        Element element = document.getElementById("wubo");
        System.out.println("element = " + element);


        System.out.println("--------------从元素中获取id--------------");
        //从元素中获取id
        System.out.println("id = " + element.id());


        System.out.println("--------------从元素中获取className--------------");
        //从元素中获取className
        System.out.println("className = " + element.className());


        System.out.println("--------------从元素中获取所有classNames--------------");
        //从元素中获取classNames
        Set<String> classNames = element.classNames();
        int i = 0;
        for (String className : classNames) {
            System.out.println("className" + ++i + " = " + className);
        }


        System.out.println("--------------从元素中获取属性的值attribute--------------");
        //从元素中获取属性的值attribute
        System.out.println("attribute = " + element.attr("id"));


        System.out.println("--------------从元素中获取所有属性的值attributes--------------");
        //从元素中获取所有属性的值attributes
        int j = 0;
        Attributes attributes = element.attributes();
        for (Attribute attribute : attributes) {
            System.out.println("attribute " + ++j + "= " + attribute);
        }


        System.out.println("--------------从元素中获取文本内容text--------------");
        //从元素中获取文本内容text
        System.out.println("text = " + element.text());

    }

    /**
    * 使用选择器获取元素
    *
    * @throws IOException
    */
    @Test
    public void selectorTest() throws IOException {
        //解析html文件湖区document对象
        Document document = Jsoup.parse(new File("C:\\Users\\ASUS\\Desktop\\index.html"), "utf8");

        //通过标签查找元素 【比如option】
        Elements elements = document.select("option");
        int a = 0;
        for (Element option : elements) {
            System.out.println("option" + ++a + "=  " + option.text());
        }

        //通过ID查找元素【#开头】
        Elements id = document.select("#wubo");
        System.out.println("id = " + id.text());

        //通过class查找元素【.开头】
        Elements classA = document.select(".class_1");
        System.out.println("classA = " + classA.text());

        //通过属性查找元素【[键]】
        //通过属性查找元素【[键=值]】
        Elements attr1 = document.select("[name]");
        Elements attr3 = document.select("[name=wubo]");
        System.out.println("attr1 = " + attr1);
        System.out.println("attr3 = " + attr3);

        //还有组合
                // 【标签+selector】下面举个例子
        Elements option = document.select("option[name]");
        System.out.println("option = " + option);

        System.out.println("------------------");
                //【元素+标签】元素下所有该标签
        Elements header = document.select("#header a");
        for (Element a标签 : header) {
            System.out.println("a标签 = " + a标签.text());
        }

        System.out.println("--------------------------");
        //【直接子元素 元素 >子元素】
        Elements select = document.select("#search-options > option");
        for (Element optiona : select) {
            System.out.println("option = " + optiona.text());
        }
        //【所有子元素 元素 >子元素 > * 】
        Elements selects = document.select("#search-options > option > *");
        for (Element optiona : selects) {
            System.out.println("option = " + optiona.text());
        }



    }



}
```