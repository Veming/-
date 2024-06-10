以下是一个简单的基于HashMap实现的支持高并发的Map类，包含save和query两个接口：

```java
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class ConcurrentMap {
    private final Map<String, Object> map = new HashMap<>();
    private final ReentrantReadWriteLock lock = new ReentrantReadWriteLock();

    public void save(String key, Object value) {
        lock.writeLock().lock();
        try {
            map.put(key, value);
            System.out.println("Saved key: " + key + ", value: " + value);
        } finally {
            lock.writeLock().unlock();
        }
    }

    public Object query(String key) {
        lock.readLock().lock();
        try {
            Object value = map.get(key);
            System.out.println("Queried key: " + key + ", value: " + value);
            return value;
        } finally { 
            lock.readLock().unlock();
        }
    }

    public static void main(String[] args) {
        ConcurrentMap concurrentMap = new ConcurrentMap();

        // Save operation in multiple threads
        for (int i = 0; i < 5; i++) {
            final int index = i;
            new Thread(() -> {
                concurrentMap.save("key" + index, "value" + index);
            }).start();
        }

        // Query operation in multiple threads
        for (int i = 0; i < 5; i++) {
            final int index = i;
            new Thread(() -> {
                concurrentMap.query("key" + index);
            }).start();
        }
    }
}
```

在上面的代码中，ConcurrentMap类使用HashMap作为存储结构，并使用ReentrantReadWriteLock实现读写锁机制来保证线程安全。save方法用于保存键值对，query方法用于查询键对应的值。在main方法中，演示了在多个线程中同时进行保存和查询操作的情况。

请注意，这只是一个简单的示例代码，实际生产环境中需要根据具体需求和并发情况做更详细的设计和优化。