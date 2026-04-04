# Configuration

### WebMvcConfigurerAdapter

Let the main application extend `WebMvcConfigurerAdapter` to obtain the default configuration, or use it as a reference:

```java
import org.springframework.boot.autoconfigure.SpringBootApplication;
import win.doyto.query.web.WebMvcConfigurerAdapter;

@SpringBootApplication
public class DemoApplication extends WebMvcConfigurerAdapter {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class);
    }
}
```

