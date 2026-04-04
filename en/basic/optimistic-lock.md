# Optimistic Lock

Make the entity object implement the `OptimisticLock` interface to support optimistic locking.

```java
public interface OptimisticLock {
    Integer currentVersion();
}
```

## Usage

```java

public class TestEntity extends AbstractPersistable<Integer> implements OptimisticLock {
    // ...

    private Integer version;

    @Column(name = "version")
    @Override
    public Integer currentVersion() {
        return version;
    }

}
```
