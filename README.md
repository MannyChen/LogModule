# LogModule
Log refs Macro

```cpp
enum {
    LOG_FATAL = 1,
    LOG_ERROR = 2,
    LOG_WARNING = 3,
    LOG_INFO = 4,
};


bool log_func(uint8_t level, const char * format, ...){
    uint64_t time = 9999; ///< replaced by the time interfaces of the platform
    char strbuf[1024] = {'\0'};
    va_list list;
    va_start(list, format);
    snprintf(strbuf, sizeof(strbuf), format, list);
    va_end(list);
    
    const char* log_type_str = "";
    switch(level){
    case LOG_FATAL:
      log_type_str     = "[FATAL]";
      break;
    case LOG_ERROR:
      log_type_str     = "[ERROR]";
      break;
    case LOG_INFO:
      log_type_str     = "[INFO ]";
      break;
    case LOG_WARNING:
      log_type_str     = "[WARN ]";
      break;
    default:
    break;
    }
    
    printf("%.3f %s %s\n", time/1000000.0f, log_type_str, strbuf);
    
    return 0;
}

#define LOG(level, ...) log_func(level, ##__VA_ARGS__)

#define LOGI(...) log_func(LOG_INFO, ##__VA_ARGS__)
#define LOGF(...) log_func(LOG_FATAL, ##__VA_ARGS__)
#define LOGW(...) log_func(LOG_WARNING, ##__VA_ARGS__)
#define LOGE(...) log_func(LOG_ERROR, ##__VA_ARGS__)

```
