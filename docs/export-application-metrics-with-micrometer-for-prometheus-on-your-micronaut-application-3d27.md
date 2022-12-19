# 在 Micronaut 应用程序上导出带有 Prometheus 测微计的应用程序指标。

> 原文：<https://dev.to/wreulicke/export-application-metrics-with-micrometer-for-prometheus-on-your-micronaut-application-3d27>

在这里，我编写了在 [Micronaut](https://docs.micronaut.io/latest/guide/index.html) 上为 Prometheus 导出应用程序指标的控制器。

## 初始化应用

创建您的应用程序。

```
$ mkdir micronaut-sandbox
$ cd micronaut-sandbox
$ sdk install micronaut #1.0.0.M4 
$ mn 
| Starting interactive mode...
| Enter a command name to run. Use TAB for completion:
mn> create-app -i -f=micrometer-prometheus
| Generating Java project.....
| Application created at /Users/user/micronaut-sandbox
| Initializing application. Please wait...
mn> create-controller Prometheus 
```

Enter fullscreen mode Exit fullscreen mode

## 为普罗米修斯创建指标端点。

添加和修改代码，如下所示。

```
package micronaut.sandbox; 
+ import io.micrometer.prometheus.PrometheusMeterRegistry;
import io.micronaut.http.annotation.Controller;
import io.micronaut.http.annotation.Get; - import io.micronaut.http.HttpStatus; 
@Controller("/prometheus")
public class PrometheusController { 
+    private final PrometheusMeterRegistry registry;

+    PrometheusController(PrometheusMeterRegistry registry) {
+        this.registry = registry;
+    } 
    @Get("/")
-  public HttpStatus index() {
-       return HttpStatus.OK;
    public String index() {
        return registry.scrape();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 启动应用程序。

启动应用程序。

```
$  ./gradlew run
Starting a Gradle Daemon, 1 incompatible Daemon could not be reused, use --status for details

# omit

> Task :run
04:05:32.293 [main] INFO  io.micronaut.runtime.Micronaut - Startup completed in 1345ms. Server Running: http://localhost:41970
<=========----> 75% EXECUTING [29s]
> :run 
```

Enter fullscreen mode Exit fullscreen mode

在其他终端中，获取度量。

```
curl http://localhost:41970/prometheus
# HELP jvm_memory_used_bytes The amount of used memory
# TYPE jvm_memory_used_bytes gauge
jvm_memory_used_bytes{area="nonheap",id="Code Cache",} 4034432.0
jvm_memory_used_bytes{area="nonheap",id="Metaspace",} 2.522096E7
jvm_memory_used_bytes{area="nonheap",id="Compressed Class Space",} 4530360.0
jvm_memory_used_bytes{area="heap",id="PS Eden Space",} 4.9322656E7
jvm_memory_used_bytes{area="heap",id="PS Survivor Space",} 0.0
jvm_memory_used_bytes{area="heap",id="PS Old Gen",} 7430040.0
# HELP jvm_classes_loaded The number of classes that are currently loaded in the Java virtual machine
# TYPE jvm_classes_loaded gauge
jvm_classes_loaded 5096.0
# HELP jvm_gc_memory_allocated_bytes_total Incremented for an increase in the size of the young generation memory pool after one GC to before the next
# TYPE jvm_gc_memory_allocated_bytes_total counter
jvm_gc_memory_allocated_bytes_total 3.5817752E7
# HELP executor_active_threads The approximate number of threads that are actively executing tasks
# TYPE executor_active_threads gauge
executor_active_threads{name="scheduled",} 0.0
executor_active_threads{name="io",} 1.0
# HELP process_start_time_seconds Start time of the process since unix epoch.
# TYPE process_start_time_seconds gauge
process_start_time_seconds 1.535223930394E9
# HELP jvm_memory_committed_bytes The amount of memory in bytes that is committed for the Java virtual machine to use
# TYPE jvm_memory_committed_bytes gauge
jvm_memory_committed_bytes{area="nonheap",id="Code Cache",} 4063232.0
jvm_memory_committed_bytes{area="nonheap",id="Metaspace",} 2.686976E7
jvm_memory_committed_bytes{area="nonheap",id="Compressed Class Space",} 4849664.0
jvm_memory_committed_bytes{area="heap",id="PS Eden Space",} 1.34217728E8
jvm_memory_committed_bytes{area="heap",id="PS Survivor Space",} 1.1010048E7
jvm_memory_committed_bytes{area="heap",id="PS Old Gen",} 1.13246208E8
# HELP jvm_threads_peak The peak live thread count since the Java virtual machine started or peak was reset
# TYPE jvm_threads_peak gauge
jvm_threads_peak 22.0
# HELP process_uptime_seconds The uptime of the Java virtual machine
# TYPE process_uptime_seconds gauge
process_uptime_seconds 120.749
# HELP executor_pool_size_threads The current number of threads in the pool
# TYPE executor_pool_size_threads gauge
executor_pool_size_threads{name="scheduled",} 8.0
executor_pool_size_threads{name="io",} 1.0
# HELP jvm_buffer_total_capacity_bytes An estimate of the total capacity of the buffers in this pool
# TYPE jvm_buffer_total_capacity_bytes gauge
jvm_buffer_total_capacity_bytes{id="direct",} 1.0
jvm_buffer_total_capacity_bytes{id="mapped",} 0.0
# HELP jvm_gc_live_data_size_bytes Size of old generation memory pool after a full GC
# TYPE jvm_gc_live_data_size_bytes gauge
jvm_gc_live_data_size_bytes 7430040.0
# HELP process_files_open The open file descriptor count
# TYPE process_files_open gauge
process_files_open 109.0
# HELP jvm_classes_unloaded_total The total number of classes unloaded since the Java virtual machine has started execution
# TYPE jvm_classes_unloaded_total counter
jvm_classes_unloaded_total 7.0
# HELP jvm_gc_memory_promoted_bytes_total Count of positive increases in the size of the old generation memory pool before GC to after GC
# TYPE jvm_gc_memory_promoted_bytes_total counter
jvm_gc_memory_promoted_bytes_total 7413656.0
# HELP jvm_buffer_count An estimate of the number of buffers in the pool
# TYPE jvm_buffer_count gauge
jvm_buffer_count{id="direct",} 2.0
jvm_buffer_count{id="mapped",} 0.0
# HELP jvm_buffer_memory_used_bytes An estimate of the memory that the Java virtual machine is using for this buffer pool
# TYPE jvm_buffer_memory_used_bytes gauge
jvm_buffer_memory_used_bytes{id="direct",} 2.0
jvm_buffer_memory_used_bytes{id="mapped",} 0.0
# HELP executor_completed_tasks_total The approximate total number of tasks that have completed execution
# TYPE executor_completed_tasks_total counter
executor_completed_tasks_total{name="scheduled",} 9.0
executor_completed_tasks_total{name="io",} 1.0
# HELP logback_events_total Number of error level events that made it to the logs
# TYPE logback_events_total counter
logback_events_total{level="error",} 0.0
logback_events_total{level="warn",} 0.0
logback_events_total{level="info",} 1.0
logback_events_total{level="debug",} 0.0
logback_events_total{level="trace",} 0.0
# HELP executor_seconds
# TYPE executor_seconds summary
executor_seconds_count{name="scheduled",} 9.0
executor_seconds_sum{name="scheduled",} 0.028088378
executor_seconds_count{name="io",} 1.0
executor_seconds_sum{name="io",} 2.06122E-4
# HELP executor_seconds_max
# TYPE executor_seconds_max gauge
executor_seconds_max{name="scheduled",} 0.026386634
executor_seconds_max{name="io",} 2.06122E-4
# HELP executor_queued_threads The approximate number of threads that are queued for execution
# TYPE executor_queued_threads gauge
executor_queued_threads{name="scheduled",} 2.0
executor_queued_threads{name="io",} 0.0
# HELP jvm_gc_max_data_size_bytes Max size of old generation memory pool
# TYPE jvm_gc_max_data_size_bytes gauge
jvm_gc_max_data_size_bytes 2.863661056E9
# HELP process_cpu_usage The "recent cpu usage" for the Java Virtual Machine process
# TYPE process_cpu_usage gauge
process_cpu_usage 0.0
# HELP system_cpu_count The number of processors available to the Java virtual machine
# TYPE system_cpu_count gauge
system_cpu_count 4.0
# HELP jvm_threads_live The current number of live threads including both daemon and non-daemon threads
# TYPE jvm_threads_live gauge
jvm_threads_live 22.0
# HELP jvm_memory_max_bytes The maximum amount of memory in bytes that can be used for memory management
# TYPE jvm_memory_max_bytes gauge
jvm_memory_max_bytes{area="nonheap",id="Code Cache",} 2.5165824E8
jvm_memory_max_bytes{area="nonheap",id="Metaspace",} -1.0
jvm_memory_max_bytes{area="nonheap",id="Compressed Class Space",} 1.073741824E9
jvm_memory_max_bytes{area="heap",id="PS Eden Space",} 1.409286144E9
jvm_memory_max_bytes{area="heap",id="PS Survivor Space",} 1.1010048E7
jvm_memory_max_bytes{area="heap",id="PS Old Gen",} 2.863661056E9
# HELP system_load_average_1m The sum of the number of runnable entities queued to available processors and the number of runnable entities running on the available processors averaged over a period of time
# TYPE system_load_average_1m gauge
system_load_average_1m 4.50048828125
# HELP jvm_threads_daemon The current number of live daemon threads
# TYPE jvm_threads_daemon gauge
jvm_threads_daemon 10.0
# HELP system_cpu_usage The "recent cpu usage" for the whole system
# TYPE system_cpu_usage gauge
system_cpu_usage 0.0
# HELP jvm_gc_pause_seconds Time spent in GC pause
# TYPE jvm_gc_pause_seconds summary
jvm_gc_pause_seconds_count{action="end of minor GC",cause="Metadata GC Threshold",} 1.0
jvm_gc_pause_seconds_sum{action="end of minor GC",cause="Metadata GC Threshold",} 0.015
jvm_gc_pause_seconds_count{action="end of major GC",cause="Metadata GC Threshold",} 1.0
jvm_gc_pause_seconds_sum{action="end of major GC",cause="Metadata GC Threshold",} 0.05
# HELP jvm_gc_pause_seconds_max Time spent in GC pause
# TYPE jvm_gc_pause_seconds_max gauge
jvm_gc_pause_seconds_max{action="end of minor GC",cause="Metadata GC Threshold",} 0.015
jvm_gc_pause_seconds_max{action="end of major GC",cause="Metadata GC Threshold",} 0.05
# HELP process_files_max The maximum file descriptor count
# TYPE process_files_max gauge
process_files_max 10240.0 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

您可以轻松导出 prometheus 的应用程序指标。

试试看，享受吧！

黑客快乐。