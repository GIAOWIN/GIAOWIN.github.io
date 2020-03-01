# 1.普通文件上传

## controller

```java
/**
 * 普通文件上传
 * @return
 */
@RequestMapping("/fileupload1")
public String fileupload1(HttpServletRequest request) throws Exception {
    System.out.println("文件上传...");

    // 使用fileupload组件完成文件上传
    // 上传的位置
    String path = request.getSession().getServletContext().getRealPath("/uploads/");
    // 判断，该路径是否存在
    File file = new File(path);
    if(!file.exists()){
        // 创建该文件夹
        file.mkdirs();
    }

    // 解析request对象，获取上传文件项
    DiskFileItemFactory factory = new DiskFileItemFactory();
    ServletFileUpload upload = new ServletFileUpload(factory);
    // 解析request
    List<FileItem> items = upload.parseRequest(request);
    // 遍历
    for(FileItem item:items){
        // 进行判断，当前item对象是否是上传文件项
        System.out.println("item = " + item);
        if(item.isFormField()){
            // 说明普通表单向
        }else{
            // 说明上传文件项
            // 获取上传文件的名称
            String filename = item.getName();
            // 把文件的名称设置唯一值，uuid
            String uuid = UUID.randomUUID().toString().replace("-", "");
            filename = uuid+"_"+filename;
            // 完成文件上传
            item.write(new File(path,filename));
            // 删除临时文件
            item.delete();
        }
    }

    return "success";
}
```

## pom

```xml
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
</dependency>

<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.4</version>
</dependency>

```

## html

```html
<form action="/user/fileupload1" method="post" enctype="multipart/form-data">
    选择文件：<input type="file" name="upload" /><br/>
    <input type="submit" value="上传" />
</form>

```



# 2.SpringMVC 文件普通上传

## controller

```java
/**
 * SpringMVC文件上传
 * @return
 */
@RequestMapping("/fileupload2")
public String fileuoload2(HttpServletRequest request, MultipartFile upload) throws Exception {
    System.out.println("fileuoload2文件上传...");

    // 上传的位置
    String path = request.getSession().getServletContext().getRealPath("/uploads/");
    // 判断，该路径是否存在
    File file = new File(path);
    if(!file.exists()){
        // 创建该文件夹
        file.mkdirs();
    }


    // 获取上传文件的名称
    String filename = upload.getOriginalFilename();
    // 把文件的名称设置唯一值，uuid
    String uuid = UUID.randomUUID().toString().replace("-", "");
    filename = uuid+"_"+filename;
    // 完成文件上传
    upload.transferTo(new File(path,filename));
    return "success";
}
```

## html

```html
<h3>Springmvc文件上传</h3>

<form action="/user/fileupload2" method="post" enctype="multipart/form-data">
    选择文件：<input type="file" name="upload" /><br/>
    <input type="submit" value="上传" />
</form>

```

# 3.SpringMVC 文件跨服务器上传

## controller

```java
/**
 * 跨服务器文件上传
 * @return
 */
@RequestMapping("/fileupload3")
public String fileuoload3(MultipartFile upload) throws Exception {
	System.out.println("跨服务器文件上传...");

	// 定义上传文件服务器路径
	String path = "http://localhost:9090/uploads/";

	// 说明上传文件项
	// 获取上传文件的名称
	String filename = upload.getOriginalFilename();
	// 把文件的名称设置唯一值，uuid
	String uuid = UUID.randomUUID().toString().replace("-", "");
	filename = uuid+"_"+filename;

	// 创建客户端的对象
	Client client = Client.create();

	// 和图片服务器进行连接
	WebResource webResource = client.resource(path + filename);

	// 上传文件
	webResource.put(upload.getBytes());

	return "success";
}
}

```

## pom

```xml
<dependency>
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-core</artifactId>
    <version>1.18.1</version>
</dependency>
<dependency>
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-client</artifactId>
    <version>1.18.1</version>
</dependency>

```

## html

```html
<h3>跨服务器文件上传</h3>

<form action="/user/fileupload3" method="post" enctype="multipart/form-data">
    选择文件：<input type="file" name="upload" /><br/>
 <input type="submit" value="上传" />
     </form>
```



