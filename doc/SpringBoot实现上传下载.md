```text
1.����web������thymeleaf����:
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
2.��дȫ�������ļ�:(application.properties)
    # thymeleaf
    spring.thymeleaf.prefix=classpath:/templates/
    spring.thymeleaf.suffix=.html
    spring.thymeleaf.mode=HTML
    spring.thymeleaf.encoding=UTF-8
    spring.thymeleaf.servlet.content-type=text/html
    spring.thymeleaf.cache=false
    # �ϴ��ļ��ܵ����ֵ
    spring.servlet.multipart.max-request-size=100MB
    # �����ļ������ֵ
    spring.servlet.multipart.max-file-size=100MB
    # �Ƿ�֧�������ϴ�(Ĭ��ֵ true)
    spring.servlet.multipart.enabled=true
    # �ϴ��ļ�����ʱĿ¼(һ������²��������޸�)
    #spring.servlet.multipart.location=
    # �ļ���С��ֵ,�����������ֵʱ��д�뵽����,��������ڴ���(Ĭ��ֵ0)
    spring.servlet.multipart.file-size-threshold=0
    # �ж��Ƿ�Ҫ�ӳٽ����ļ�(�൱��������,һ������²��������޸�)
    spring.servlet.multipart.resolve-lazily=false
3.�����ϴ����ص�Ŀ¼: \src\main\resources\file\
4.��дController�����ϴ�:
    @Controller
    public class FileUploadController {
        private Logger logger = LoggerFactory.getLogger(FileUploadController.class);
        //�ļ�·��
        //private String path = "D:\\datafile\\";
        private String path;
        {
            try {
                path = new ClassPathResource("/file/").getFile().getPath() + java.io.File.separator;
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        @GetMapping("/upload")
        public String singleFile() {return "upload";}
        @GetMapping("/uploadBatch")
        public String multipleFiles() {return "uploadBatch";}
        @PostMapping("/upload")
        @ResponseBody
        public String upload(MultipartFile file){
            // �жϷǿ�
            if(file.isEmpty()){return "�ϴ����ļ�����Ϊ��";}
            try {
                // ����MultipartFile�ӿڵķ���
                logger.info("[�ļ�����ContentType] - [{}]",file.getContentType());
                logger.info("[�ļ��������Name] - [{}]",file.getName());
                logger.info("[�ļ�ԭ����OriginalFileName] - [{}]",file.getOriginalFilename());
                logger.info("[�ļ���С] - [{}]",file.getSize());
                logger.info("path is "+path);
                File f = new File(path);
                if(!f.exists()){f.mkdir();}
                logger.info(path + file.getOriginalFilename());
                File dir = new File(path + file.getOriginalFilename());
                // ����transferTo����,Ҳ�����ֽ����ķ�ʽ�ϴ��ļ�,�����ֽ����Ƚ���(������transferTo)
                file.transferTo(dir);
                // writeFile(file);
                return "�ϴ������ļ��ɹ�";
            } catch (Exception e){
                e.printStackTrace();
                return "�ϴ������ļ�ʧ��";
            }
        }
        @PostMapping("/uploadBatch")
        @ResponseBody
        public String uploadBatch(MultipartFile[] files){
            if(files != null && files.length > 0){
                for (MultipartFile mf : files) {
                    // ��ȡԴ�ļ�����
                    String filename = mf.getOriginalFilename();
                    // �жϷǿ�
                    if(mf.isEmpty()){ return "�ļ�����: "+ filename+ " �ϴ�ʧ��,ԭ�����ļ�Ϊ�գ�"; }
                    File dir = new File(path + filename);
                    try {
                        // д���ļ�
                        mf.transferTo(dir);
                        logger.info("�ļ�����: " + filename + "�ϴ��ɹ�");
                    }catch (Exception e){
                        logger.error(e.toString(),e);
                        return "�ļ�����: "+ filename + " �ϴ�ʧ��";
                    }
                }
                return "���ļ��ϴ��ɹ�";
            }
            return "���ļ��ϴ�ʧ��";
        }
        private void writeFile(MultipartFile file){
            try {
                // ��ȡ�����
                OutputStream os = new FileOutputStream(path + file.getOriginalFilename());
                // ��ȡ������ (CommonsMultipartFile �п���ֱ�ӵõ��ļ�����)
                InputStream is = file.getInputStream();
                byte[] buffer = new byte[1024];
                // �ж��������е������Ƿ��Ѿ�����ı�ʶ
                int length = 0;
                // ѭ�������������뻺���� (len=in.read(buffer))>0�ͱ�ʾin���滹������
                while ((length = is.read(buffer)) != -1){
                    // ʹ���������������������д��ָ��Ŀ¼���ļ�(savePath+"\\"+filename)��
                    os.write(buffer,0,length);
                }
                os.flush();
                os.close();
                is.close();
            }catch (Exception e){
                e.printStackTrace();
            }
        }
    }
5.��д�򵥵��ϴ�ҳ��:
    [1]���ļ��ϴ�: (upload.html)
        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="UTF-8"/>
            <title>���ļ��ϴ�</title>
        </head>
        <body>
            <p>���ļ��ϴ�</p>
            <form method="post" enctype="multipart/form-data" action="/upload">
                �ļ�: <input type="file" name="file"/>
                <input type="submit"/>
            </form>
        </body>
        </html>
    [2]���ļ��ϴ�: (uploadBatch.html)
        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="UTF-8">
            <title>���ļ��ϴ�</title>
        </head>
        <body>
            <p>���ļ��ϴ�</p>
            <form method="post" enctype="multipart/form-data" action="/uploadBatch">
                <p>�ļ�1: <input type="file" name="files"/></p>
                <p>�ļ�2: <input type="file" name="files"/></p>
                <p><input type="submit" value="�ϴ�"/></p>
            </form>
        </body>
        </html>
6.��дController��������:
    @Controller
    public class FileDownloadController {
        private Logger logger = LoggerFactory.getLogger(FileDownloadController.class);
        @GetMapping("/download")
        public String download(){ return "download"; }
        @GetMapping("/downloadfile")
        @ResponseBody
        public String downloadfile(@RequestParam String fileName,
            HttpServletRequest request, HttpServletResponse response) throws IOException {
            if(fileName != null && !"".equals(fileName)){
                Resource resource;
                String file;
                try {
                    // ��ȡ�������ļ���
                    resource = new ClassPathResource("/file/"+fileName);
                    file = resource.getFile().getPath();
                }catch (Exception e){
                    e.printStackTrace();
                    return "�ļ�������";
                }
                File f = new File(file);
                if(f.exists()){
                    // ������Ӧ����
                    response.setContentType("multipart/form-data");
                    response.addHeader("Content-Disposition","attachment; fileName="
                        + fileName +";filename*=utf-8 "+ URLEncoder.encode(fileName,"UTF-8"));
                    byte[] buffer = new byte[1024];
                    FileInputStream fis = null;
                    BufferedInputStream bis = null;
                    try {
                        fis = new FileInputStream(f);
                        bis = new BufferedInputStream(fis);
                        ServletOutputStream os = response.getOutputStream();
                        int len = bis.read(buffer);
                        logger.info("the size of buffer is "+ len);
                        while (len != -1) {
                            os.write(buffer,0, len);
                            len = bis.read(buffer);
                        }
                        return "���سɹ�";
                    }catch (Exception e){
                        e.printStackTrace();
                    }finally {
                        if(bis != null){
                            try {
                                bis.close();
                            } catch (IOException e){
                                e.printStackTrace();
                            }
                        }
                        if(fis != null){
                            try {
                                fis.close();
                            }catch (IOException e){
                                e.printStackTrace();
                            }
                        }
                    }
                }
            }
            return "�ļ�������";
        }
    }
7.��д�򵥵�����ҳ��: (download.html)
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>�ļ�����</title>
        <script src="https://code.jquery.com/jquery-3.1.1.min.js"></script>
    </head>
    <body>
        <label for="fileName">���ص��ļ�����</label><input id="fileName" type="text"/>
        <a href="javascript:void(0);" onclick="down()">�����ļ�</a>
        <a id="download" style="display:none"></a>
    <script>
        function down() {
            var fileName = $("#fileName").val().toString();
            console.log(fileName);
            var $a = document.getElementById("download");
            $a.setAttribute("href","/downloadfile?fileName="+fileName);
            $a.click();
        }
    </script>
    </body>
    </html>
```