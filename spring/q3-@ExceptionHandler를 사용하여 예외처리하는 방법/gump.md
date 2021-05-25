# q3-@ExceptionHandler를 사용하여 예외처리하는 방법

# **정리**

### **@Exceptionhandler란?**

웹 요청은 리퀘스트를 통해 들어와요. 즉 메인 로직은 컨트롤러 메서드 실행시 동작해요.

이때, 일어나는 예외를 잡아줘요.

<br>

## **@ExceptionHandler**

```java
@RestController
@CrossOrigin(origins = "*", allowedHeaders = "*")
public class MemberController {
    private MemberService memberService;

    public MemberController(MemberService memberService) {
        this.memberService = memberService;
    }

    @PostMapping("/members")
    public ResponseEntity createMember(@RequestBody MemberRequest request) {
        MemberResponse member = memberService.createMember(request);
        return ResponseEntity.created(URI.create("/members/" + member.getId())).build();
    }

    @ExceptionHandler(value = {InvalidInputException.class, DuplicateException.class} )
    public ResponseEntity<String> inputValid(RuntimeException exception) {
        return ResponseEntity.badRequest().body(exception.getMessage());
    }
}
```

위와 같이, 컨트롤러 단 내부에서 예외를 잡아줄 수 있어요. 이때의 적용범위는 MemberController 한곳이에요.

### **사용법**

컨트롤러에서 일어날만한 예외를 `@Exceptionhandler`의 value 옵션으로 넣어줘요. 확인할 수 있듯이 여러 예외를 하나의 메소드로 처리할 수 있어요. 

하지만 컨트롤러가 많아지고, 예외가 많아지면 관리 포인트가 늘어나겠고, 코드량이 많아지겠죠..? 

이때 사용하는 것이  `@ControllerAdvice`에요.

<br>

## **@ControllerAdvice**

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    private Logger logger = LoggerFactory.getLogger(GlobalExceptionHandler.class);

    @ExceptionHandler(value = {InvalidInputException.class, DuplicateException.class})
    public ResponseEntity<String> unValidBinding(final MethodArgumentNotValidException exception) {
        logger.error(exception.getMessage());
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(exception.getMessage());
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> unExpectException(final Exception exception) {
        logger.error(exception.getMessage());
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(exception.getMessage());
    }
}
```

`@Controller`, `@RestController`가 적용된 Bean내에서 발생하는 예외를 잡아서 하나의 메서드에서 처리해주는 기능을 하는 애노테이션이에요.

이제 Handler단의 전반적인 예외를 잡을 수 있게 됐어요. 

잠깐, 뭔가 이상하지 않나요?

### **@ControllerAdvice  ≠ @RestControllerAdvice**

분명,  제목을 `@ControllerAdivce`라 했는데, 실제 사용은 `@RestControllerAdivce`로 했어요. 

> 이유는, 위의 예제에서 @RestController를 사용하고 있기 때문이에요

`@RestControllerAdvice`는 `@ControllerAdivce`를 포함하고 있지만, 그 반대로는 포함하고 있지 않기에, `@RestController`사용시에는 `@RestControllerAdvice`를 사용해야해요

<br>

# **질문**

## **나만 알만한 것**

### **질문**

```java
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ResponseStatus;

@ResponseStatus(HttpStatus.BAD_REQUEST)
public class InvalidInputException extends SubwayException {
    public InvalidInputException(String message) {
        super(message);
    }
}
```

위와 같은 코드가 있을때, @ExceptionHandler를 대신 해주는 것일까요?

그렇지 않다면, 어떻게 사용해야 할까요? 

### **답변**

<br>

## **진짜 궁금한 것**

### **질문**

@ExceptionHandler의 적용범위