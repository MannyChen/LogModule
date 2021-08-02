# LogModule
Log refs Macro

```cpp
enum {
    FATAL = 1,
    ERROR = 2,
    WARNING = 3,
    INFO = 4,
};


bool log_func(uint8_t level, const char * format, ...){
    uint64_t time = 9999; ///< replaced by the time interfaces of the platform
    char strbuf[1024] = {'\0'};
    va_list list;
    va_start(list, format);
    snprintf(strbuf, sizeof(strbuf), format, list);
    va_end(list);
    
    const char* ctrl_color_start = "";
    const char* log_type_str = "";
    const char* ctrl_color_end = "\x1B[0m";
    switch(level){
    case: FATAL
      ctrl_color_start = "\x1b[35m";
      log_type_str     = "[FATAL]";
      break;
    case: ERROR
      ctrl_color_start = "\x1b[31m";
      log_type_str     = "[ERROR]";
      break;
    case: INFO
      ctrl_color_start = "\x1b[0m";
      log_type_str     = "[INFO ]";
      break;
    case: WARNING
      ctrl_color_start = "\x1b[33m";
      log_type_str     = "[WARN ]";
      break;
    default:
    break;
    }
    
    printf("%s%.3f %s %s %s", ctrl_color_start, time/1000000.0f, log_type_str, strbuf, ctrl_color_end);
}

#define LOG(level, ...) log_func(level, ##_VA_ARGS__)

#define LOGI(...) log_func(INFO, ##_VA_ARGS__)
#define LOGF(...) log_func(FATAL, ##_VA_ARGS__)
#define LOGW(...) log_func(WARNING, ##_VA_ARGS__)
#define LOGE(...) log_func(ERROR, ##_VA_ARGS__)

```
