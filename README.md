# LogModule
Log refs Macro


1. 将以下内容复制到工程代码当中
2. 日志分四个等级，包装好宏了，最好把系统时间替换掉，带上时间分析日志更好
3. 将日志模块连接到你们主控的调试串口就可以了，模块的文件系统会自动存到txt
4. 控制好日志的存储频率，不要太快了。10hz左右的传感器数据，文本日志记录一些重要的事件信息
5. 硬件连接5v电源 外加主控的TX连接到日志模块的RX

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
