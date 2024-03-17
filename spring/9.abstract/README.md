## API 추상화

### Controller
```java
@RestController
@RequestMapping("/api/board")
@RequiredArgsConstructor
public class BoardApiController extends CRUDApiAbstractController<BoardDTO, BoardEntity> {
}
```

### CRUDApiAbstractController
```java
public abstract class CRUDApiAbstractController<DTO, ENTITY> implements CRUDInteface<DTO> {

    @Autowired(required = false)
    private CRUDAbstractService<DTO, ENTITY> crudAbstractService;

    @PostMapping("")
    @Override
    public DTO create(@Valid @RequestBody DTO dto) {
        return crudAbstractService.create(dto);
    }

    @GetMapping("/id/{id}")
    @Override
    public Optional<DTO> read(@PathVariable Long id) {
        return crudAbstractService.read(id);
    }

    @PutMapping("")
    @Override
    public DTO update(@Valid @RequestBody DTO dto) {
        return crudAbstractService.update(dto);
    }

    @DeleteMapping("")
    @Override
    public void delete(@PathVariable Long id) {
        crudAbstractService.delete(id);
    }

    @GetMapping("/all")
    @Override
    public Api<List<DTO>> list(@PageableDefault Pageable pageable) {
        return crudAbstractService.list(pageable);
    }
}
```

### CRUDInterface
```java
public interface CRUDInteface<DTO> {
    DTO create(DTO dto);

    Optional<DTO> read(Long id);

    DTO update(DTO dto);

    void delete(Long id);

    Api<List<DTO>> list(Pageable pageable);
}
```

### CRUDAbstractService
```java
public abstract class CRUDAbstractService<DTO, ENTITY> implements CRUDInteface<DTO> {

    @Autowired(required = false)
    private JpaRepository<ENTITY, Long> jpaRepository;

    @Autowired(required = false)
    private Converter<DTO, ENTITY> converter;

    @Override
    public DTO create(DTO dto) {
        // dto -> entity
        var entity = converter.toEntity(dto);

        // entity -> save
        jpaRepository.save(entity);

        // save -> dto
        var returnDTO = converter.toDTO(entity);

        return returnDTO;
    }

    @Override
    public Optional<DTO> read(Long id) {

        var optionalEntity = jpaRepository.findById(id);
        var dto = optionalEntity.map(
            it -> converter.toDTO(it)
        ).orElseGet(() -> null);

        return Optional.ofNullable(dto);
    }

    @Override
    public DTO update(DTO dto) {
        var entity = converter.toEntity(dto);
        jpaRepository.save(entity);
        var returnDTO = converter.toDTO(entity);
        return returnDTO;
    }

    @Override
    public void delete(Long id) {
        jpaRepository.deleteById(id);
    }

    @Override
    public Api<List<DTO>> list(Pageable pageable) {
        var list = jpaRepository.findAll(pageable);

        var pagination = Pagination.builder()
                .page()
                .size()
                .currentElements()
                .totalElements()
                .totalPage()
                .build();

        var dtoList = list.stream()
                .map(it -> converter.toDTO(it))
                .toList();

        var response = Api.<List<DTO>>builder()
                .body(dtoList)
                .pagination(pagination)
                .build();

        return response;
    }
}
```

### Converter
```java
public interface Converter<DTO, ENTITY> {
    DTO toDTO(ENTITY entity);

    ENTITY toEntity(DTO dto);
}
```

### BoardConverter
```java
@Service
@RequiredArgsConstructor
public class BoardConverter implements Converter<BoardDTO, BoardEntity> {

    private final BoardRepository boardRepository;

    @Override
    public BoardDTO toDTO(BoardEntity boardEntity) {

        return BoardDTO.builder()
                .id(boardEntity.getId())
                .boardName(boardEntity.getBoardName())
                .status(boardEntity.getStatus())
                .build();
    }

    @Override
    public BoardEntity toEntity(BoardDTO boardDTO) {

        var boardEntity = boardRepository.findById(boardDTO.getId());

        return BoardEntity.builder()
                .id(boardDTO.getId())
                .boardName(boardDTO.getBoardName())
                .status(boardDTO.getStatus())
                .build();
    }
}
```