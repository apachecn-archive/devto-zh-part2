# 我需要帮助重构数据库模块，所有的创建，插入等。代码在它们各自的文件中。

> 原文：<https://dev.to/seanballais/i-need-help-refactoring-the-database-module-where-all-the-creation-insertion-etc-code-are-in-their-own-respective-files-2cl1>

注意:我被允许在这里发布代码。

我目前正在实习。我们正在维护一个在组织中广泛使用的系统。它是用 Java 写的。我被分配去重构代码，我选择了数据库模块，认为这是一个简单的工作。

当我看到代码时，它充满了单线，我觉得这个模块可以做得更好。模块中的一个类是`InsertDatabase`。它处理每个数据插入。看起来是这样的:

```
public class InsertDatabase {
    private static InsertDatabase instance = null;

    private Properties prop = new Properties();
    private InputStream input = null;

    private InsertDatabase(){
        try{
            input = new FileInputStream("textfile/sql_queries.txt");
            prop.load(input);
        }catch(Exception e){
            System.err.println(e);
        }
    }

    public static InsertDatabase getInstance(){
        if(instance == null) {
            instance = new InsertDatabase();
        }
        return instance;
    }

    public void insertIntoCourseTable(String course_abbr, String course_name) {
        Connection conn = cpInstance.getConnection();
        try(PreparedStatement ps = conn.prepareStatement(prop.getProperty("insert_course_table"));) {
            ps.setString(1, course_abbr);
            ps.setString(2, course_name);
            ps.executeUpdate();

            Helper.getInstance().aprintln(this,Helper.LOG_VERBOSE,"");
            Helper.getInstance().aprintln(this,Helper.LOG_VERBOSE,"*** Information Added into Course Table***");
        } catch (SQLException e) {
            Helper.getInstance().aprintln(this,Helper.LOG_VERBOSE,"Data already exists");
        }finally {
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }

    // more code...
} 
```

Enter fullscreen mode Exit fullscreen mode

分配给表创建、更新数据和选择数据的其他类也是如此。

SQL 查询实际上存储在名为`sql_queries.txt`的文本文件中，并加载到变量`props`中。这个文本文件差不多有 200 行长。这是文本文件中的一个示例行。

```
insert_diagnostic_table = insert into diagnostic_table (date_start, date_end, sem_num, freetime, time_penalty, balance_penalty) values (?, ?, ?, ?, ?, ?)
insert_pasa_balance_table = insert into pasa_balance_table (date_rendered, sender, amount, current_balance_sender, deducted_balance_sender, receiver, current_balance_receiver, added_balance_receiver) values (curdate(), ?, ?, ?, ?, ?, ?, ?) 
```

Enter fullscreen mode Exit fullscreen mode

然后这些类被聚集成一个名为`Information`的单例类，系统用它来与数据库交互。类是这样的:

```
public class Information {
    private static Information instance = null;

    private InsertDatabase insert_db = InsertDatabase.getInstance();
    private SearchDatabase search_db = SearchDatabase.getInstance();
    private UpdateDatabase update_db = UpdateDatabase.getInstance();
    private DeleteDatabase delete_db = DeleteDatabase.getInstance();
    private TransDatabase trans_db = TransDatabase.getInstance();

    public static Information getInstance(){
        if(instance == null) {
            instance = new Information();
        }
        return instance;
    }

    /*
     * Here lies all of the methods that access DeleteDatabase
     */

    public void storeToDeleteReservation(String username) {
        delete_db.deleteReservation(username);
    }

    // more code...

    public void storeStudentToDelete(String username) {
        delete_db.deleteStudent(username);
    }

    /**
     * Deletes Staff
     * @param username
     */
    public void storeStaffToDelete(String username) {
        delete_db.deleteStaff(username);
    }

    /*
     * End of methods that access DeleteDatabase
     */

    /*
     * Here lies all the methods that access InsertDatabase
     */

    public void storeAccountInfo(String username, String password, String last_name, String first_name, String mid_name, String suffix_name){
        insert_db.insertIntoAccountTable(username, password, last_name, first_name, mid_name, suffix_name);
    }

    public void storeCourseInfo(String course_abbr, String course_name) {
        insert_db.insertIntoCourseTable(course_abbr, course_name);
    }

    // more code...
} 
```

Enter fullscreen mode Exit fullscreen mode

我有种奇怪的感觉，这个有问题。我不能确切指出问题是什么。让他们单身对我来说已经是一个警告了。那么，这种方法有什么问题吗？

我想做的是为每个表创建一个 DAO(数据访问对象),让系统的其余部分使用 DAO。如果我继续使用 DAOs，我会首先修改`Information`,这样我就不会碰到系统的其余部分，也不会意外地破坏任何东西。如果我真的打碎了什么东西，至少我知道我在哪里搞砸了。你怎么想呢?

谢谢！:)