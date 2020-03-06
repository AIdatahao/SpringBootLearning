```text
1.����web������test����:
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
2.��д������:
    @Configuration
    @EnableAsync
    public class ExecutorConfig {
        @Bean
        public Executor asynServiceExcutor(){
            ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
            // ���ú����߳���
            executor.setCorePoolSize(5);
            // ��������߳���
            executor.setMaxPoolSize(10);
            // ���ö��д�С
            executor.setQueueCapacity(100);
            // �����̳߳����߳����ֵ�ǰ׺
            executor.setThreadNamePrefix("asynTaskThread-");
            // ���þܾ�����
            executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
            // �̳߳س�ʼ��
            executor.initialize();
            return executor;
        }
    }
3.��дservice�ӿ�:
    public interface AsyncService {
        void writeTest(CountDownLatch countDownLatch);
    }
4.��дservice�ӿڵ�ʵ����:
    @Service
    public class AsyncServiceImpl implements AsyncService {
        private Logger logger = LoggerFactory.getLogger(AsyncServiceImpl.class);
        @Override
        @Async("asynServiceExcutor")    // �������ж���Ĳ���ִ����
        public void writeTest(CountDownLatch countDownLatch) {
            try {
                logger.info("�߳�[{}]��ʼִ��", Thread.currentThread().getId());
                Thread.sleep(1000);
                logger.info("�߳�[{}]ִ�н���", Thread.currentThread().getId());
            }catch (Exception e){
                e.printStackTrace();
            }finally {
                countDownLatch.countDown();
            }
        }
    }
5.��д���Է���:
    @Autowired
    AsyncService asyncService;
    @Test
    public void test1() throws InterruptedException {
        int count = 60;
        CountDownLatch countDownLatch = new CountDownLatch(count);
        for (int i = 0; i <= count; i++){
            asyncService.writeTest(countDownLatch);
        }
        // ��ֹ���߳�ִ������ʱ,���߳��Ƚ���
        countDownLatch.await();
    }
```