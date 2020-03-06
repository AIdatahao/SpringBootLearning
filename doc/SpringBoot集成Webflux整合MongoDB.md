```text
1.����webflux��reactive mongodb����:
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-webflux</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb-reactive</artifactId>
    </dependency>
2.����Reactive MongoDB: (������������@EnableReactiveMongoRepositoriesע��)
    @SpringBootApplication
    @EnableReactiveMongoRepositories
    public class WebfluxApplication {
        public static void main(String[] args) {
            SpringApplication.run(WebfluxApplication.class, args);
        }
    }
3.��yml�����ļ�������MongoDB����:
    spring:
        data:
            mongodb:
                host: localhost
                # ����MongoDBʹ�õĶ˿ں�
                port: 27017
                # MongoDB�в�����database,��Ҫ����
                database: webflux
4.Mongo Shell����Mongo Compass���ߴ������ݿ�webflux,������user�ĵ�:
    (1)ʹ��Mongo Shell:
        1)�������ݿ�webflux: use webflux
        2)����user�ĵ�: db.createCollection(user)
    (2)ʹ��Mongo Compass���ߴ������ݿ�: ͼ�λ�����
5.����Userʵ����:
    @Document(collection = "user")
    public class User {
        @Id
        private String id;
        private String name;
        private Integer age;
        private String description;
        // get set ��
    }
6.��дMapper�ӿ�: (ReactiveMongoRepository�ṩ�ķ���������Ӧʽ��)
    @Repository
    public interface UserDao extends ReactiveMongoRepository<User, String> {
    }
7.��дService�ӿ�:
    public interface UserService {
        // ��ѯ�ĵ�����������
        Flux<User> selectAll();
        // ͨ��id��ѯ�ĵ��ڵ�����
        Mono<User> selectById(String id);
        // ���ĵ��ڲ���һ����¼
        Mono<User> create(User user);
        // ͨ��id�����ĵ�����
        Mono<User> updateById(String id,User user);
        // ͨ��idɾ���ĵ�����
        Mono<Void> deleteById(String id);
    }
8.��дServiceʵ����:
    @Service
    public class UserServiceImpl implements UserService {
        @Autowired
        private UserMapper mapper;
        @Override
        public Flux<User> selectAll() {
            return this.mapper.findAll();
        }
        @Override
        public Mono<User> selectById(String id) {
            return this.mapper.findById(id);
        }
        @Override
        public Mono<User> create(User user) {
            return this.mapper.save(user);
        }
        @Override
        public Mono<User> updateById(String id, User user) {
            return this.mapper.findById(id)
                .flatMap(
                    u -> {
                        u.setName(user.getName());
                        u.setAge(user.getAge());
                        u.setDescription(user.getDescription());
                        return this.mapper.save(user);
                    }
            );
        }
        @Override
        public Mono<Void> deleteById(String id) {
            return this.mapper.findById(id).flatMap(user -> this.mapper.delete(user));
        }
    }
9.��дController:
    @RestController
    public class UserController {
        @Autowired
        private UserService service;
        @GetMapping("/user")
        public Flux<User> getUsers(){
            return this.service.selectAll();
        }
        @GetMapping("/user/{id}")
        public Mono<ResponseEntity<User>> getUser(@PathVariable String id){
            return this.service.selectById(id)
                    .map(user -> new ResponseEntity<>(user, HttpStatus.OK))
                    .defaultIfEmpty(new ResponseEntity<>(HttpStatus.NOT_FOUND));
        }
        @PostMapping("/user")
        public Mono<User> createUser(User user){
            return this.service.create(user);
        }
        @PutMapping("/user/{id}")
        public Mono<ResponseEntity<User>> updateUser(@PathVariable String id,User user){
            return this.service.updateById(id,user)
                    .map(u -> new ResponseEntity<>(u,HttpStatus.OK))
                    .defaultIfEmpty(new ResponseEntity<>(HttpStatus.NOT_FOUND));
        }
        @DeleteMapping("/user/{id}")
        public Mono<ResponseEntity<Void>> deleteUser(@PathVariable String id){
            return this.service.deleteById(id)
                    .then(Mono.just(new ResponseEntity<Void>(HttpStatus.OK)))
                    .defaultIfEmpty(new ResponseEntity<>(HttpStatus.NOT_FOUND));
        }
    }
```