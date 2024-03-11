## MemoryDB

### Repository
```java
// Repository
public interface Repository<T, ID> {

}
```

```java
// DataRepository
public interface DataRepository<T, ID> extends Repository<T, ID> {

    T save(T data);

    Optional<T> findById(ID id);

    List<T> findAll();

    void delete(ID id);

}
```

```java
abstract public class SimpleDataRepository<T extends Entity, ID extends Long> implements DataRepository<T, ID>{
    private final List<T> dataList = new ArrayList<>();

    private static long index = 0;

    private final Comparator<T> sort = Comparator.comparingLong(Entity::getId);

    @Override
    public T save(T data) {
        if(Objects.isNull(data)){
            throw new RuntimeException("Null");
        }

        var prevData = dataList.stream()
                        .filter(it -> it.getId().equals(data.getId())).findFirst();

        if(prevData.isPresent()){
            //prevedata : Optinal<UserEntity> 그래서 remove에 넣으면 안지워진다 -> prevData.get으로 가져오기
            dataList.remove(prevData.get());
            dataList.add(data);
        }else{
            index ++;
            data.setId(index);
            dataList.add(data);
        }

        return data;
    }

    @Override
    public Optional<T> findById(ID id) {
        return dataList.stream()
                .filter(it -> it.getId().equals(id)).findFirst();
    }

    @Override
    public List<T> findAll() {
        return dataList.stream()
                .sorted(sort)
                .toList();
    }

    @Override
    public void delete(ID id) {
        var deleteEntity = dataList.stream()
                .filter(it -> it.getId().equals(id)).findFirst();

        deleteEntity.ifPresent(dataList::remove);
    }
}
```

### Entity
```java
public interface PrimaryKey {
    void setId(Long id);
    Long getId();
}
```

```java
@Getter
@Setter
public abstract class Entity implements PrimaryKey{
    private Long id;
}
```

### Controller
```java
@Controller
@RequestMapping("/user")
@RequiredArgsConstructor
public class UserApiController {

    private final UserService userService;

    @PutMapping("")
    public UserEntity create(@RequestBody UserEntity userEntity){
        return userService.save(userEntity);
    }

    @GetMapping("/all")
    public List<UserEntity> findAll(){
        return userService.findAll();
    }

    @DeleteMapping("/id/{id}")
    public void delete(@PathVariable Long id){
        userService.delete(id);
    }

    @GetMapping("/id/{id}")
    public UserEntity findById(@PathVariable Long id){
        var response =  userService.findById(id);
        return response.get();
    }

    @GetMapping("/score")
    public List<UserEntity> findById(@RequestParam int score){
        return userService.filterScore(score);
    }
}
```

### Service
```java
@Service
@RequiredArgsConstructor
public class UserService {

    private final UserRepository userRepository;

    public UserEntity save(UserEntity user){
        return userRepository.save(user);
    }

    public List<UserEntity> findAll(){
        return userRepository.findAll();
    }

    public void delete(Long id){
        userRepository.delete(id);
    }

    public Optional<UserEntity> findById(Long id){
        return userRepository.findById(id);
    }

    public List<UserEntity> filterScore(int score){
        return userRepository.findAllScoreGreaterThen(score);
    }
}
```

### Repository
```java
@Service
public class UserRepository extends SimpleDataRepository<UserEntity, Long> {
    public List<UserEntity> findAllScoreGreaterThen(int score){
        return this.findAll().stream()
                .filter(it -> it.getScore() >= score)
                .toList();
    }
}
```

### Config
```java
@Configuration
public class DatabaseConfig {

    @Bean
    public UserRepository userRepository(){
        return new UserRepository();
    }

}
```




