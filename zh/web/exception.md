# 异常断言

### 定义

```java
public interface ErrorCode {

    Integer getCode();

    String getMessage();
    
    ...

    static void assertNotNull(Object target, ErrorCode errorCode, Object... messages) {
        assertFalse(target == null, errorCode, messages);
    }

    static void assertTrue(boolean condition, ErrorCode errorCode, Object... messages) {
        assertFalse(!condition, errorCode, messages);
    }

    static void assertFalse(boolean condition, ErrorCode errorCode, Object... messages) {
        if (condition) {
            fail(errorCode, messages);
        }
    }

    static void fail(ErrorCode errorCode, Object... messages) {
        Logger logger = LoggerFactory.getLogger(ErrorCode.class);
        if (logger.isWarnEnabled()) {
            logger.warn("[{}]{} {}", errorCode.getCode(), errorCode.getMessage(), StringUtils.join(messages, ", "));
        }
        throw new ErrorCodeException(errorCode);
    }
}
```

### 用法

```java
public void patch(R request) {
    E e = buildEntity(request);
    int count = service.patch(e);
    ErrorCode.assertTrue(count == 1, PresetErrorCode.ENTITY_NOT_FOUND);
}
```

### 返回值

```json
{
  "code": "9",
  "message": "查询记录不存在"
}
```

