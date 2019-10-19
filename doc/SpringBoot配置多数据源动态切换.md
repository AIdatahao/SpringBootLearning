1.����mybatis������oracle����������mysql����������druid����Դ����������aop����:
	<dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>1.3.2</version>
    </dependency>
	<dependency>
        <groupId>com.oracle</groupId>
        <artifactId>ojdbc7</artifactId>
        <version>12.1.0.2</version>
    </dependency>
	<dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
	<dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
        <version>1.1.10</version>
    </dependency>
	<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-aop</artifactId>
    </dependency>
2.��application.yml�ļ������ö�����Դ:
	# ��������Ŀ��·��
	server:
		servlet:
			context-path: /web
	spring:
		datasource:
			druid:
				master:
					type: com.alibaba.druid.pool.DruidDataSource
					driver-class-name: com.mysql.cj.jdbc.Driver
					url: jdbc:mysql://localhost:3306/test?serverTimezone=GMT%2B8
					username: root
					password: root
					# ���ӳص�����
					initial-size: 5
					min-idle: 5
					max-active: 20
					# ���ӵȴ���ʱʱ��
					max-wait: 30000
					# ���ü����ԹرյĿ������Ӽ��ʱ��
					time-between-eviction-runs-millis: 60000
					# ���������ڳ��е���С����ʱ��
					min-evictable-idle-time-millis: 300000
					validation-query: select '1' from dual
					test-while-idle: true
					test-on-borrow: false
					test-on-return: false
					# ��PSCache,����ָ��ÿ�������ϵ�PSCache�Ĵ�С
					pool-prepared-statements: true
					max-open-prepared-statements: 20
					max-pool-prepared-statement-per-connection-size: 20
					# ���ü��ͳ�����ص�filters,ȥ�����ؽ����sql�޷�ͳ��,'wall'���ڷ���ǽ
					filters: stat,wall
				slave:
					ds1:
						type: com.alibaba.druid.pool.DruidDataSource
						driver-class-name: oracle.jdbc.OracleDriver
						url: jdbc:oracle:thin:@localhost:1521:orcl
						username: scott
						password: tigger
						# ���ӳص�����
						initial-size: 5
						min-idle: 5
						max-active: 20
						# ���ӵȴ���ʱʱ��
						max-wait: 30000
						# ���ü����ԹرյĿ������Ӽ��ʱ��
						time-between-eviction-runs-millis: 60000
						# ���������ڳ��е���С����ʱ��
						min-evictable-idle-time-millis: 300000
						validation-query: select '1' from dual
						test-while-idle: true
						test-on-borrow: false
						test-on-return: false
						# ��PSCache,����ָ��ÿ�������ϵ�PSCache�Ĵ�С
						pool-prepared-statements: true
						max-open-prepared-statements: 20
						max-pool-prepared-statement-per-connection-size: 20
						# ���ü��ͳ�����ص�filters,ȥ�����ؽ����sql�޷�ͳ��,'wall'���ڷ���ǽ
						filters: stat,wall
			# ���ü��ͳ�����ص�filters,ȥ�����ؽ����sql�޷�ͳ��,'wall'���ڷ���ǽ(�����Զ��������Դ����������sql�޷�ͳ��)
			#filters: stat,wall
			# Spring��ص�AOP�����,��x.y.z.service.*,���ö��Ӣ�Ķ��ŷָ�
			aop-patterns: com.springboot.service.*
		
			#WebStatFilter����
			web-stat-filter:
				enabled: true
				# ��ӹ��˹���
				url-pattern: /*
				# ���Թ��˵ĸ�ʽ
				exclusions: '*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*'
		
			#StatViewFilter����
			stat-view-servlet:
				enabled: true
				#���ý���Druid��ؽ��������
				url-pattern: /druid/*
				#�Ƿ��ܹ���������
				reset-enable: false
				#���÷���Druid����̨���˻�������
				#login-username: admin
				#login-password: admin
				# IP������
				#allow: 127.0.0.1
				# IP����������ͬ����ʱ��deny������allow��
				#deny: 192.168.1.218
			#����StatFilter
			filter:
				stat:
					log-slow-sql: true
				wall:
					config:
						multi-statement-allow: true
	mybatis:
		# type-aliasesɨ��·��
		# type-aliases-package:
		# mapper xmlʵ��ɨ��·��
		mapper-locations: classpath:mapper/*.xml
3.��д����Դ��·����:
	public class DynamicDataSource extends AbstractRoutingDataSource {
		//�����е�determineCurrentLookupKey����ȡ��һ���ַ���,
		//���ַ������������ļ��е���Ӧ�ַ�������ƥ���Զ�λ����Դ
		@Override
		protected Object determineCurrentLookupKey() {
			//DynamicDataSourceContextHolder������ʹ��setDataSourceType ���õ�ǰ������Դ,
			//��·������ʹ��getDataSourceType���л�ȡ,����AbstractRoutingDataSource����ע��ʹ��
			return DynamicDataSourceContextHolder.getDataSourceType();
		}
	}
4.��д��̬����Դ��������:
	public class DynamicDataSourceContextHolder {
		//��ʹ��ThreadLocalά������ʱ,ThreadLocalΪÿ��ʹ�øñ������߳��ṩ�����ı�������,
		//����ÿһ���̶߳����Զ����ظı��Լ��ĸ���,������Ӱ�������߳�����Ӧ�ĸ���
		private static final ThreadLocal<String> contextHolder = new ThreadLocal<String>();
		//�������е�����Դid;��Ҫ��Ϊ���ж�����Դ�Ƿ����;
		public static List<String> dataSourceIds = new ArrayList<String>();
		//��������Դ
		public static void setDataSourceType(String dataSourceType) { contextHolder.set(dataSourceType); }
		//��ȡ����Դ
		public static String getDataSourceType() { return contextHolder.get(); }
		//�������Դ
		public static void clearDataSourceType() { contextHolder.remove(); }
		//�ж�ָ��DataSrouce��ǰ�Ƿ����
		public static boolean containsDataSource(String dataSourceId){
			return dataSourceIds.contains(dataSourceId);
		}
	}
5.��д��̬����Դע����:
    // ��Ҫ�����������ӵ���ע��: @Import(DynamicDataSourceRegister.class)
	public class DynamicDataSourceRegister implements ImportBeanDefinitionRegistrar, EnvironmentAware {
		//ָ��Ĭ������Դ(springboot2.0Ĭ������Դ��hikari�����ʹ����������Դ�����Լ�����)
		private static final String DATASOURCE_TYPE_DEFAULT = "com.zaxxer.hikari.HikariDataSource";
		//Ĭ������Դ
		private DataSource defaultDataSource;
		//�û��Զ�������Դ
		private Map<String, DataSource> slaveDataSources = new HashMap<>();
		private final static ConfigurationPropertyNameAliases aliases = new ConfigurationPropertyNameAliases(); //����
		static {
			//���ڲ�������Դ���ò�ͬ�������ڴ˴���ӱ����������л�����Դ����ĳЩ�����޷�ע������
			aliases.addAliases("url", new String[]{"jdbc-url"});
			aliases.addAliases("username", new String[]{"user"});
		}
		@Override
		public void setEnvironment(Environment environment) {
			initDefaultDataSource(environment);
			initslaveDataSources(environment);
		}
		private void initDefaultDataSource(Environment env) {
			// ��ȡ������Դ
			Binder binder = Binder.get(env);
			Map dsMap = binder.bind("spring.datasource.druid.master", Map.class).get();
			defaultDataSource = buildDataSource(dsMap);
		}
		private void initslaveDataSources(Environment env) {
			// ��ȡ�����ļ���ȡ��������Դ
			Binder binder = Binder.get(env);
			HashMap map = binder.bind("spring.datasource.druid.slave", HashMap.class).get();
			for (Object o : map.entrySet()) {
				Map.Entry entry = (Map.Entry) o;
				String dsPrefix = (String) entry.getKey();
				Map<String, Object> dsMap = (Map<String, Object>) entry.getValue();
				DataSource ds = buildDataSource(dsMap);
				slaveDataSources.put(dsPrefix, ds);
			}
		}
		@Override
		public void registerBeanDefinitions(AnnotationMetadata annotationMetadata, BeanDefinitionRegistry beanDefinitionRegistry) {
			Map<Object, Object> targetDataSources = new HashMap<>();
			//���Ĭ������Դ
			targetDataSources.put("dataSource", this.defaultDataSource);
			DynamicDataSourceContextHolder.dataSourceIds.add("dataSource");
			//�����������Դ
			targetDataSources.putAll(slaveDataSources);
			DynamicDataSourceContextHolder.dataSourceIds.addAll(slaveDataSources.keySet());
			//����DynamicDataSource
			GenericBeanDefinition beanDefinition = new GenericBeanDefinition();
			beanDefinition.setBeanClass(DynamicDataSource.class);
			beanDefinition.setSynthetic(true);
			MutablePropertyValues mpv = beanDefinition.getPropertyValues();
			mpv.addPropertyValue("defaultTargetDataSource", defaultDataSource);
			mpv.addPropertyValue("targetDataSources", targetDataSources);
			//ע�� - BeanDefinitionRegistry
			beanDefinitionRegistry.registerBeanDefinition("dataSource", beanDefinition);
		}
		private DataSource buildDataSource(Map dataSourceMap) {
			try {
				Object type = dataSourceMap.get("type");
				if (type == null) { type = DATASOURCE_TYPE_DEFAULT;} // Ĭ��DataSource 
				Class<? extends DataSource> dataSourceType;
				dataSourceType = (Class<? extends DataSource>) Class.forName((String) type);
				ConfigurationPropertySource source = new MapConfigurationPropertySource(dataSourceMap);
				Binder binder = new Binder(source.withAliases(aliases));
				return binder.bind(ConfigurationPropertyName.EMPTY, Bindable.of(dataSourceType)).get(); //ͨ�����Ͱ󶨲��������ʵ������
			} catch (ClassNotFoundException e) {
				e.printStackTrace();
			}
			return null;
		}
	}
6.��дָ������Դע����:
	@Target({ ElementType.METHOD, ElementType.TYPE })
	@Retention(RetentionPolicy.RUNTIME)
	@Documented
	public @interface TargetDataSource {
		String value();
	}
7.��д�л�����ԴAdvice��:
	@Aspect
	@Order(-1)//��֤��AOP��@Transactional֮ǰִ��
	@Component
	public class DynamicDataSourceAspect {
		/*
		* @Before("@annotation(targetDataSource)"):
		*   @Before��ע�ķ�������@targetDataSource��ע�ķ���֮ǰִ��
		*/
		@Before("@annotation(targetDataSource)")
		public void changeDataSource(JoinPoint point, TargetDataSource targetDataSource) throws Throwable {
			//��ȡ��ǰ��ָ��������Դ;
			String dsId = targetDataSource.value();
			//�����������ע������е�����Դ��Χ֮�ڣ���ô���������Ϣ��ϵͳ�Զ�ʹ��Ĭ�ϵ�����Դ��
			if (!DynamicDataSourceContextHolder.containsDataSource(dsId)) {
				System.err.println("����Դ[{}]������,ʹ��Ĭ������Դ > {}"+targetDataSource.value()+point.getSignature());
			} else {
				System.out.println("Use DataSource : {} > {}"+targetDataSource.value()+point.getSignature());
				//�ҵ��Ļ�,��ô���õ���̬����Դ��������
				DynamicDataSourceContextHolder.setDataSourceType(targetDataSource.value());
			}
		}
		@After("@annotation(targetDataSource)")
		public void restoreDataSource(JoinPoint point, TargetDataSource targetDataSource) {
			System.out.println("Revert DataSource : {} > {}"+targetDataSource.value()+point.getSignature());
			//����ִ�����֮��,���ٵ�ǰ����Դ��Ϣ,������������
			DynamicDataSourceContextHolder.clearDataSourceType();
		}
	}
8.������Դ��̬�л�����:
	[1]�������ݿ����������(mysql5.5,oracle11g)
		Mysql:
			DROP TABLE IF EXISTS `student`;
			CREATE TABLE `student` (
			`sno` varchar(3) NOT NULL,
			`sname` varchar(9) NOT NULL,
			`ssex` char(2) NOT NULL,
			`database` varchar(10) DEFAULT NULL
			) DEFAULT CHARSET=utf8;
			INSERT INTO `student` VALUES ('001', '����', 'M', 'mysql');
			INSERT INTO `student` VALUES ('002', '���', 'M', 'mysql');
		Oracle:
			DROP TABLE "SCOTT"."STUDENT";
			CREATE TABLE "SCOTT"."STUDENT" (
			"SNO" VARCHAR2(3 BYTE) NOT NULL ,
			"SNAME" VARCHAR2(9 BYTE) NOT NULL ,
			"SSEX" CHAR(2 BYTE) NOT NULL ,
			"database" VARCHAR2(10 BYTE) NULL 
			);
			INSERT INTO "SCOTT"."STUDENT" VALUES ('001', 'KangKang', 'M ', 'oracle');
			INSERT INTO "SCOTT"."STUDENT" VALUES ('002', 'Mike', 'M ', 'oracle');
			INSERT INTO "SCOTT"."STUDENT" VALUES ('003', 'Jane', 'F ', 'oracle');
			INSERT INTO "SCOTT"."STUDENT" VALUES ('004', 'Maria', 'F ', 'oracle');
	[2]��дMapper�ӿ�:
		@Component
		@Mapper
		public interface StudentMapper {
			List<Map<String, Object>> getAllStudents();
		}
	[3]��дMapper��ʵ��:
	    // ��Ҫ������������xml�ļ���ɨ��ע��: @MapperScan("com.springboot.dao")
		<?xml version="1.0" encoding="UTF-8" ?>
		<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
				"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
		<mapper namespace="com.example.multidatasource.dao.StudentMapper">
			<select id="getAllStudents" resultType="java.util.Map">
				select * from student
			</select>
		</mapper>
	[4]��дService�ӿ�:
		public interface StudentService {
			List<Map<String,Object>> getAllStudentsFromMysql();
			List<Map<String,Object>> getAllStudentsFromOracle();
		}
	[5]��дService��ʵ����:
		@Service("studentService")
		public class StudentServiceImp implements StudentService {
			@Autowired
			private StudentMapper studentMapper;
			@Override
			@Transactional	//������Դ������(bug:������Դ����ʹ�ø�ע��)
			public List<Map<String, Object>> getAllStudentsFromMaster() {
				return this.studentMapper.getAllStudents();
			}
			@Override
			@TargetDataSource("ds1")
			public List<Map<String, Object>> getAllStudentsFromSlave() {
				return this.studentMapper.getAllStudents();
			}
		}
	[6]��дController:
		@RestController
		public class DataSourceController {
			@Autowired
			private StudentService studentService;
			@RequestMapping("/querystudentsfromoracle")
			public List<Map<String,Object>> queryStudentsFromOracle(){
				return this.studentService.getAllStudentsFromSlave();
			}
			@RequestMapping("/querystudentsfrommysql")
			public List<Map<String,Object>> queryStudentsFromMysql(){
				return this.studentService.getAllStudentsFromMaster();
			}
		}
	[7]����MySQL����Դ: http://localhost:8080/web/querystudentsfrommysql
       ����Oracle����Դ: http://localhost:8080/web/querystudentsfromoracle