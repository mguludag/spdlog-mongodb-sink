# spdlog-mongodb-sink
mongodb sink for spdlog library

## Dependencies
* [spdlog](https://github.com/gabime/spdlog)
* [mongocxx](http://mongocxx.org/mongocxx-v3/installation/)

## Usage samples
### asynchronous logger
```C++
#include <spdlog/async.h>
#include <spdlog/sinks/mongo_sink.h>
#include <spdlog/spdlog.h>
#include <thread>

int main() {
  std::thread t1([&] {
    auto logging = spdlog::async_factory::create<spdlog::sinks::mongo_sink>(
        "mongo_logger_1", "db", "collection");
    for (auto i = 0; i < 10; ++i) {
      logging->info("thread_1 iter: {}",i);
      std::this_thread::sleep_for(std::chrono::milliseconds(500));
    }
  });

  std::thread t2([&] {
    auto logging = spdlog::async_factory::create<spdlog::sinks::mongo_sink>(
        "mongo_logger_2", "db", "collection");
    for (auto i = 0; i < 10; ++i) {
      logging->info("thread_2 iter: {}",i);
      std::this_thread::sleep_for(std::chrono::milliseconds(100));
    }
  });

  std::this_thread::sleep_for(std::chrono::seconds(10));

  t1.join();
  t2.join();

  return 0;
}
```

### synchronous logger
```C++
#include <spdlog/sinks/mongo_sink.h>
#include <spdlog/spdlog.h>
#include <thread>

int main() {
    auto logging = spdlog::create<spdlog::sinks::mongo_sink>(
        "mongo_logger", "db", "collection");
  std::thread t1([&] {
    for (auto i = 0; i < 10; ++i) {
      logging->info("thread_1" + std::to_string(i));
      std::this_thread::sleep_for(std::chrono::milliseconds(500));
    }
  });

  std::thread t2([&] {
    for (auto i = 0; i < 10; ++i) {
      logging->info("thread_2" + std::to_string(i));
      std::this_thread::sleep_for(std::chrono::milliseconds(100));
    }
  });

  std::this_thread::sleep_for(std::chrono::seconds(10));

  t1.join();
  t2.join();

  return 0;
}
```

### Example document from mongodb
```JSON
{
  "_id": {
    "$oid": "60d87492314f000087006980"
  },
  "timestamp": {
    "$date": "2021-06-27T12:52:34.985Z"
  },
  "level": "info",
  "message": "thread_1 5",
  "logger_name": "mongo_logger1",
  "thread_id": 18892
}
```
