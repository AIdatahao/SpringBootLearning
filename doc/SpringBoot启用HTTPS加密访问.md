```text
1.��ȡHTTPS֤��:
    [1]��������� HTTPS ֤����Ҫ��֤����Ȩ���Ļ��,������õ�֤��ž��й�����,Ҳ�ᱻ����������ͻ������Ͽ�;
    [2]������֤��Ʒ��: Symantec,GeoTrustm,TrustAsia,Symantec ��;
    [3]��ʹ��Java�Դ���keytool����HTTPS֤��:
        (1)keytool����ʹ��˵��: 
            D:\>keytool
            ��Կ��֤�������
            ����:
             -certreq            ����֤������
             -changealias        ������Ŀ�ı���
             -delete             ɾ����Ŀ
             -exportcert         ����֤��
             -genkeypair         ������Կ��
             -genseckey          ������Կ
             -gencert            ����֤����������֤��
             -importcert         ����֤���֤����
             -importpass         �������
             -importkeystore     ��������Կ�⵼��һ����������Ŀ
             -keypasswd          ������Ŀ����Կ����
             -list               �г���Կ���е���Ŀ
             -printcert          ��ӡ֤������
             -printcertreq       ��ӡ֤�����������
             -printcrl           ��ӡ CRL �ļ�������
             -storepasswd        ������Կ��Ĵ洢����
        (2)keytool -genkeypair����˵��:
            D:\>keytool -genkeypair --help
            keytool -genkeypair [OPTION]...
            ������Կ��
            ѡ��:
             -alias <alias>                  Ҫ�������Ŀ�ı���
             -keyalg <keyalg>                ��Կ�㷨����
             -keysize <keysize>              ��Կλ��С
             -sigalg <sigalg>                ǩ���㷨����
             -destalias <destalias>          Ŀ�����
             -dname <dname>                  Ψһ�б���
             -startdate <startdate>          ֤����Ч�ڿ�ʼ����/ʱ��
             -ext <value>                    X.509 ��չ
             -validity <valDays>             ��Ч����
             -keypass <arg>                  ��Կ����
             -keystore <keystore>            ��Կ������
             -storepass <arg>                ��Կ�����
             -storetype <storetype>          ��Կ������
             -providername <providername>    �ṩ������
             -providerclass <providerclass>  �ṩ������
             -providerarg <arg>              �ṩ������
             -providerpath <pathlist>        �ṩ����·��
             -v                              ��ϸ���
             -protected                      ͨ���ܱ����Ļ��ƵĿ���
    [4]ʹ��keytool������ǩ��֤��: (���������һ·Enter(���Ǳ���)�����ȷ������һ��"��")
        D:\>keytool -genkeypair -alias springboot_https -keypass 123456 -keyalg RSA 
            -keysize 1024 -validity 365 -keystore d:/springboot_https.keystore -storepass 123456
        ����������������ʲô?
        [Unknown]:  
        ������֯��λ������ʲô?
        [Unknown]:  
        ������֯������ʲô?
        [Unknown]:  
        �����ڵĳ��л�����������ʲô?
        [Unknown]:  
        �����ڵ�ʡ/��/������������ʲô?
        [Unknown]:  
        �õ�λ��˫��ĸ����/����������ʲô?
        [Unknown]:  
        CN=null, OU=null, O=null, L=null, ST=null, C=null�Ƿ���ȷ?
        [��]:  ��
        D:\>  
    [5]�鿴���ɵ�֤����Ϣ: (��Կ��������JKS,�����õ�)
        D:\>keytool -list -keystore springboot_https.keystore
        ������Կ�����:
        
        ��Կ������: JKS
        ��Կ���ṩ��: SUN
        
        ������Կ����� 1 ����Ŀ
        
        springboot_https, 2020-3-5, PrivateKeyEntry,
        ֤��ָ�� (SHA1): F1:EF:DF:ED:0A:8F:DA:13:B5:86:65:4E:FE:A1:87:1F:03:51:06:4D
        D:\>
    (�Լ����ɵ� HTTPS ֤��ֻ�������Լ�����,��������������ʱ,��������޷���ʾ֤����Ϣ)
2.���� HTTPS ֤��:
    [1]�����ɵ�HTTPS֤���ļ�������Springboot��Ŀ��src/main/resourceĿ¼��:
    [2]application.yml�ļ�������HTTPS�����Ϣ:
        # ���� HTTPS �����Ϣ
        server:
          port: 443
          # Ϊ�˺��������ʹ��,��ʱ����
          http-port: 80
          ssl:
            enabled: true
            # ֤���ļ�
            key-store: classpath:springboot_https.keystore
            # ����
            key-password: 123456
            # ��Կ������
            key-store-type: JKS
            key-alias: springboot_https
4.����HTTPS֤��:
    [1]��дcontroller:
        @RestController
        public class HttpsController {
            @GetMapping(value = "/hello")
            public String hello() {
                return "Hello HTTPS";
            }
        }
    [2]������Ŀ,����: https://localhost/hello
    (�������Լ����ɵ�֤��,����ʾ����ȫ,�������ʼ���)
5.HTTP��תHTTPS:
    [1]HTTPS�Ѿ����Է�����,����HTTPȴ���ܷ���,������������������HTTPS֮��,
        ����ϣ��HTTP��������Զ���ת��HTTPS;
    [2]��д�������HTTP����ֱ��ת����HTTPS:
        @Configuration
        public class Http2Https {
            @Value("${server.port}")
            private int sslPort;
            @Value("${server.http-port}")
            private int httpPort;
            @Bean
            public TomcatServletWebServerFactory servletContainerFactory() {
                TomcatServletWebServerFactory tomcat = new TomcatServletWebServerFactory() {
                    @Override
                    protected void postProcessContext(Context context) {
                        SecurityConstraint securityConstraint = new SecurityConstraint();
                        securityConstraint.setUserConstraint("CONFIDENTIAL");
                        SecurityCollection collection = new SecurityCollection();
                        collection.addPattern("/*");
                        securityConstraint.addCollection(collection);
                        context.addConstraint(securityConstraint);
                    }
                };
                Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
                connector.setScheme("http");
                connector.setPort(httpPort);
                connector.setRedirectPort(sslPort);
                tomcat.addAdditionalTomcatConnectors(connector);
                return tomcat;
            }
        }
    [3]�ٴ�����֮��,ʹ�� http://localhost/hello ���ʻ��Զ���ת�� https://localhost/hello.
6.���֤������: (��������Ѷ�����������)
    [1]��Ѱ� DV SSL ֤������: https://cloud.tencent.com/document/product/400/35224
    [2]��װ֤��:
        (1)Apache ������֤�鰲װ:
            https://cloud.tencent.com/document/product/400/35243
        (2)Nginx ������֤�鰲װ:
            https://cloud.tencent.com/document/product/400/35244
        (3)Tomcat ������֤�鰲װ:
            https://cloud.tencent.com/document/product/400/35224
        (4)Windows IIS ������֤�鰲װ:
            https://cloud.tencent.com/document/product/400/35225
```