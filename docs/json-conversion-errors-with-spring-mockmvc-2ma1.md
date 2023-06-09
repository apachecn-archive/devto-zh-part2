# Spring MockMvc 的 Json 转换错误

> 原文：<https://dev.to/piczmar_0/json-conversion-errors-with-spring-mockmvc-2ma1>

MockMvc 是一个 Spring 类，用于单元测试控制器，无需启动服务器。它有一个流畅的 API 来断言响应消息。例如:对于 JSON 响应，我们可以这样写断言:

```
 this.mockMvc.perform(get("/test"))
                .andExpect(content().contentType(MediaType.APPLICATION_JSON_UTF8_VALUE))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.value", is("Hello!"))); 
```

底下用的是 [`jsonPath`](https://github.com/json-path/JsonPath) 。

对于字符串值断言，它工作得很好，但是对于数值，它的行为可能会很奇怪。

这里有一个例子:

测试控制器时的方法:

```
 public static final double VALUE = 0.07185454505642408;

...

    @GetMapping(value = "/test", produces = MediaType.APPLICATION_JSON_UTF8_VALUE)
    public ResponseEntity get() {
        Dto dto = new Dto();
        dto.setValue(VALUE);
        return new ResponseEntity(dto, HttpStatus.OK);
    } 
```

使用测试:

```
@RunWith(SpringRunner.class)
@WebMvcTest(ExampleController.class)
public class ExampleControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    public void should_get_metadata_when_video_processed() throws Exception {
        this.mockMvc.perform(get("/test"))
                .andDo(print())
                .andExpect(content().contentType(MediaType.APPLICATION_JSON_UTF8_VALUE))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.value", is(ExampleController.VALUE)));
    }

} 
```

测试失败:

```
java.lang.AssertionError: JSON path "$.value"
Expected: is <0.07185454505642408>
     but: was <0.07185454505642408>
Expected :is <0.07185454505642408>

Actual   :<0.07185454505642408> 
```

哇！打印的期望值和实际值是相同的。
到底怎么回事？

事实上，问题在于预期和实际的类型。
预期是`Double`，实际是`BigDecimal`。

如果我没有调试过它，我不会知道它。

原因是 MockMvc 正在使用一个`JsonSmartJsonProvider`进行序列化。

当值很小时，例如，对于 Long，它可以向下转换为 Integer，对于 BigDecimal，它可以向下转换为 Double，这就是`JsonSmartJsonProvider`的工作方式。

当我们将特定类型的值序列化为 JSON，但无法将其反序列化回相同的数值类型时，会导致意外情况。

那么什么是解决方案呢？

可以使用另一个 JSON 提供者——一个预测性的`JacksonJsonProvider`,因为
您可以配置它总是将数值反序列化为 Long 和 Double。

可惜不是 jsonPath 中使用的默认实现。

[这里的](https://github.com/piczmar/spring-mockmvc-error)是一个小的 Github 项目，有一些测试演示了`JsonSmartJsonProvider`的问题以及如何用`JacksonJsonProvider`解决它们

简而言之，解决方案很简单。

您需要在您的测试初始化中添加这个配置:

```
 final ObjectMapper objectMapper = new ObjectMapper();
        objectMapper.enable(DeserializationFeature.USE_LONG_FOR_INTS);
        objectMapper.enable(DeserializationFeature.USE_BIG_DECIMAL_FOR_FLOATS);

        Configuration.setDefaults(new Configuration.Defaults() {

            private final JsonProvider jsonProvider = new JacksonJsonProvider(objectMapper);
            private final MappingProvider mappingProvider = new JacksonMappingProvider(objectMapper);

            @Override
            public JsonProvider jsonProvider() {
                return jsonProvider;
            }

            @Override
            public MappingProvider mappingProvider() {
                return mappingProvider;
            }

            @Override
            public Set<Option> options() {
                return EnumSet.noneOf(Option.class);
            }
        }); 
```

通过设置`DeserializationFeature`，你得到的整数值总是被反序列化为`Long`，并且总是浮动到`BigDecimal`。

希望这能帮助你节省一些时间来解决我遇到的奇怪错误。