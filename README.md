# LogModule
Log refs Macro

目的：
1. 记录下问题场景时候关键数据用于问题分析;
<br/>

软件部分建议:
1. 以下的几个宏就基本可以满足各位的需求了, glog/openlog到头来也就是调用几个宏，用起来再慢慢读源码模仿增加骚操作的接口，不同频率输出/变化才输出等等；
2. 日志分等级（进阶可以用ANSI Escape Sequences/Code颜色等级区分，e.g.红色高亮<font color=red> [ERROR] error happened, pls check</font>）
3. 包装好宏了，把系统时间替换掉，带上时间（系统运行时间/北京时间）分析很重要；
4. 保证一下跨平台的使用，在都支持c++的前提下可以用虚函数重新实现各自的输出；
5. MCU需要学习日志模块的文件系统，学会重启日志模块新建文件等的操作，一般的淘宝的串口sd卡模块就直接文件系统写好了，只需要printf到串口，可以先用一下别人现成的，感受下痛点再去设计自己的日志模块；
6. linux/windows 直接用系统接口读写文件；
7. 2维数据（传感器数据等）、文本日志都要存重要事件，二维数据可以通过数据提取的小软件重新plot出来波形；
<br/>

硬件部分建议：
1. MCU侧日志模块需要考虑USB读卡系统，车上出了问题，直接插typeC数据线可以读到有的日志文件
2. MCU侧的日志模块最好映射到两个输出端，一个写到文件系统里，一个直接通过调试串口printf
3. 在线的二维数据输出和文本日志printf,考虑下重新改写那个上位机（林亮洪做的），保证文本日志的输出和二维数据的输出
<br/>

e.g.

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
    
    ///< replaced by the interfaces in the running os, uart/SPI in mcu, file interfaces in windows/linux
    write2file("%.3f %s %s\n", time/1000000.0f, log_type_str, strbuf);  ///< for log file
    printf("%.3f %s %s\n", time/1000000.0f, log_type_str, strbuf);      ///< for online debug
    
    return 0;
}

///< be happy with the API below
#define LOG(level, ...) log_func(level, ##__VA_ARGS__)

#define LOGI(...) log_func(LOG_INFO, ##__VA_ARGS__)
#define LOGF(...) log_func(LOG_FATAL, ##__VA_ARGS__)
#define LOGW(...) log_func(LOG_WARNING, ##__VA_ARGS__)
#define LOGE(...) log_func(LOG_ERROR, ##__VA_ARGS__)

```
