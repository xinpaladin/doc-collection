|     | micrometer metrics                  | 描述                                             |
| --- | ----------------------------------- | ------------------------------------------------ |
| 1   | jvm_buffer_count_buffers jvm        | 缓冲区计数缓冲区                                 |
| 2   | jvm_buffer_memory_used_bytes        | jvm缓冲区内存已用字节                            |
| 3   | jvm_buffer_total_capacity_bytes     | jvm缓冲区总容量字节                              |
| 4   | jvm_classes_loaded_classes          | jvm_classes加载的类                              |
| 5   | jvm_classes_unloaded_classes_total  | 自Java虚拟机开始执行以来已卸载的类总数           |
| 6   | jvm_gc_max_data_size_bytes          | jvm_gc_最大数据大小字节                          |
| 7   | jvm_gc_memory_allocated_bytes_total | 在一个GC之后到下一个GC之前增加年轻代内存池的大小 |
| 8   | jvm_gc_memory_promoted_bytes_total  | GC之前到GC之后，老年代的大小正向增加的计数       |
| 9   | system_cpu_count                    | Java虚拟机可用的处理器数量                       |
| 10  | process_uptime_seconds              | Java虚拟机的正常运行时间                         |
| 11  | jvm_threads_states_threads          | 当前处于NEW状态的线程数                          |
| 12  | jvm_memory_committed_bytes          | 可供Java虚拟机使用的已提交的内存量               |
| 13  | system_cpu_usage                    | 最近的cpu利用率                                  |
| 14  | jvm_threads_peak_threads            | 自Java虚拟机启动或重置峰值以来的活动线程峰值     |
| 15  | jvm_memory_used_bytes               | 已用内存量                                       |
| 16  | jvm_threads_daemon_threads          | 当前活动的守护程序线程数                         |
| 17  | process_cpu_usage                   | JVM的CPU利用率                                   |
| 18  | process_start_time_seconds          | 进程的开始时间                                   |
| 19  | jvm_gc_max_data_size_bytes          | 老年代的最大内存量                               |
| 20  | jvm_gc_live_data_size_bytes         | full GC老年代的大小                              |
| 21  | jvm_threads_live_threads            | 当前活动线程数，包括守护程序线程和非守护程序线程 |
| 22  | jvm_buffer_memory_used_bytes        | 已使用缓冲池大小                                 |
| 23  | jvm_buffer_count_buffers            | 缓冲区数量                                       |