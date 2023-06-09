# 你见过最诡异的编码实践是什么？

> 原文：<https://dev.to/makiten/whats-the-weirdest-coding-practice-youve-seen-366j>

受 [kodengo 的帖子](https://dev.to/kodengo_com/whats-the-most-irritating-if-statement-condition-that-youve-come-across-27jf)启发，你在代码中见过最诡异的事是什么？

对我来说，我管理一个 CRUD 应用程序，它使用 Oracle 作为数据库。我们混合了纯查询和存储过程，所以在应用程序中(。NET)，我们必须创建一堆参数。

几乎每个执行查询和过程的文件都有这样的设置，但是有一个文件有超过 30 个参数！类似于。

```
OracleParameter prm_FNAME = new OracleParameter("FIRST_NAME", OracleType.Char, 32);
prm_FNAME.SourceColumn = "FIRST_NAME";
prm_FNAME.Direction = ParameterDirection.Input;
prm_FNAME.IsNullable = true;
prm_FNAME.Value = strFirstName;

OracleParameter prm_LNAME = new OracleParameter("LAST_NAME", OracleType.Char, 32);
prm_LNAME.SourceColumn = "LAST_NAME";
prm_LNAME.Direction = ParameterDirection.Input;
prm_LNAME.IsNullable = true;
prm_LNAME.Value = strLastName;

...

OracleParameter prm_RETVAL = new OracleParameter("RET_VAL", OracleType.Char);
prm_RETVAL.Direction = ParameterDirection.Output;

List<OracleParameter> params = new List<OracleParameter>();
params.Add(prm_FNAME);
params.Add(prm_LNAME);
...
params.Add(prm_RETVAL);

for (int i = 0; i < params.Length; i++) {
    oraCommand.Parameters.Add(params[i]);
}

oraCommand.ExecuteNonQuery();

string value = params[params.Length - 1].Value;

return value; 
```

Enter fullscreen mode Exit fullscreen mode

我已经很多年没有在这个地方工作了，但我仍然记得这样的代码。经过询问，我后来发现

1.  没有人发现这个代码库有任何问题
2.  团队中没有人知道什么是`Dictionary`

你见过最糟糕的事情是什么？