# 春季批次- JPA 到 XLSX，带 Apache POI

> 原文：<https://dev.to/wpanas/spring-batch-jpa-to-xlsx-with-apache-poi-4lf1>

# 什么是阿帕奇 POI？

Apache POI 是一个 Java 库，允许读写微软文档。它支持 Excel、Word、PowerPoint 甚至 Visio。

在本文中，我将重点介绍 Excel 及其当前的文件格式 XLSX。Apache POI 提供了低内存占用 API SXSSF，用于写入 XLSX 文件。您可以通过设置滑动窗口来指定在内存中处理多少行。如果行数超过了窗口的大小，它们将被刷新到临时文件中。最后，所有数据都被传输到你指定的目标文件中。

Spring Framework 有自己的库来处理大量数据。它被称为春批。Spring Batch 支持 CSV 文件，但是本身不支持 XLSX 文件。您可以在 Apache POI SXSSF API 的支持下实现将数据存储到 XLSX。

# 春季批量 SXSSF 示例

假设你有一家书店。您还有一个应用程序，它允许您存储有关待售书籍的信息。你想订购新书。您的递送人希望您给他发送一个 XLSX 文件，其中包含要递送的图书列表。

下面是你的书店的应用程序逻辑的 JPA 表示。您有一个`Book`模型和一个在数据库中保存图书信息的存储库。

`@Data`注释是[项目龙目岛](https://projectlombok.org/features/Data)的一部分。它为所有属性生成`toString`、`equals`、
、`hashCode`、getters 和 setters 方法。

```
@Data
@Entity
public class Book {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String title;
    private String author;
    private String isbn;
} 
```

```
@Repository
public interface BookRepository extends PagingAndSortingRepository<Book, Long> {
} 
```

为了处理来自 JPA 存储库的数据，您需要创建一个`RepositoryItemReader`和一个支持批处理和 JPA 存储库的配置。

```
@Configuration
@EnableBatchProcessing
@EnableJpaRepositories
public class BatchConfiguration {
    private static final int CHUNK = 100;

    @Bean
    public ItemReader<Book> bookReader(BookRepository repository) {
        RepositoryItemReader<Book> reader = new RepositoryItemReader<>();
        reader.setRepository(repository);
        reader.setMethodName("findAll");
        reader.setPageSize(CHUNK);
        reader.setSort(singletonMap("id", Sort.Direction.ASC));
        return reader;
    }
} 
```

获取的书籍将被写入电子表格。所有的电子表格都保存在一个`Workbook`中，所以在此之前你必须创建一个。

```
@Bean
public SXSSFWorkbook workbook() {
    return new SXSSFWorkbook(CHUNK);
} 
```

`SXSSFWorkbook`存储在内存中的行数被明确设置为`CHUNK`。使用`Workbook`，你可以创建一个名为`Books`的电子表格。它将被传递给`BookWriter`。

```
@Bean
public ItemWriter<Book> bookWriter(SXSSFWorkbook workbook) {
    SXSSFSheet sheet = workbook.createSheet("Books");
    return new BookWriter(sheet);
} 
```

`BookWriter`是一个为存储库中的每本书
向电子表格添加新行的类。书籍属性保存为列中的单元格。

```
public class BookWriter implements ItemWriter<Book> {
    private final Sheet sheet;

    BookWriter(Sheet sheet) {
        this.sheet = sheet;
    }

    @Override
    public void write(List<? extends Book> list) {
        for (int i = 0; i < list.size(); i++) {
            writeRow(i, list.get(i));
        }
    }

    private void writeRow(int currentRowNumber, Book book) {
        List<String> columns = prepareColumns(book);
        Row row = this.sheet.createRow(currentRowNumber);
        for (int i = 0; i < columns.size(); i++) {
            writeCell(row, i, columns.get(i));
        }
    }

    private List<String> prepareColumns(Book book) {
        return asList(
                book.getId().toString(),
                book.getAuthor(),
                book.getTitle(),
                book.getIsbn()
        );
    }

    private void writeCell(Row row, int currentColumnNumber, String value) {
        Cell cell = row.createCell(currentColumnNumber);
        cell.setCellValue(value);
    }
} 
```

要完成从 JPA 存储库到 XLSX 文件的图书传输，您需要创建一个`Job`。每个`Job`至少有一个`Step`要做。

```
@Bean
public Step step(ItemReader<Book> reader, ItemWriter<Book> writer) {
    return stepBuilderFactory.get("export")
            .<Book, Book>chunk(CHUNK)
            .reader(reader)
            .writer(writer)
            .build();
}

@Bean
public Job job(Step step, JobExecutionListener listener) {
    return jobBuilderFactory.get("exportBooksToXlsx")
            .start(step)
            .listener(listener)
            .build();
} 
```

当`Job`状态改变时，调用`JobExecutionListener`。完成`Job`后，您可以使用`FileOutputStream`和 dispose created `Workbook`将图书从`Workbook`保存到选定的 XLSX 文件中。

```
@Slf4j
public class JobListener implements JobExecutionListener {
    private final SXSSFWorkbook workbook;
    private final FileOutputStream fileOutputStream;

    JobListener(SXSSFWorkbook workbook, FileOutputStream fileOutputStream) {
        this.workbook = workbook;
        this.fileOutputStream = fileOutputStream;
    }

    @Override
    public void afterJob(JobExecution jobExecution) {
        BatchStatus batchStatus = jobExecution.getStatus().getBatchStatus();
        if (batchStatus == COMPLETED) {
            try {
                workbook.write(fileOutputStream);
                fileOutputStream.close();
                workbook.dispose();
            } catch (IOException e) {
                log.error(e.getMessage(), e);
            }
        }
    }

    @Override
    public void beforeJob(JobExecution jobExecution) {

    }
} 
```

# 总结

Spring Batch 非常有弹性，它可以帮助你用任何可用的库处理大数据块。这个例子展示了如何使用 Apache POI 来实现这一点，但是您可以根据自己的目的轻松地对其进行更改。记住通过设置块来最大化你的性能。当 RAM 内存将被超过时，数据处理的速度将显著下降。

为本文创建的项目可以在 [GitHub](https://github.com/wpanas/code-snippets/tree/master/jpa-to-xlsx) 上找到。