# 使用 gql 的 Python GraphQL 客户端请求示例

> 原文：<https://dev.to/hugo__df/python-graphql-client-requests-example-using-gql-523g>

> 一个使用 gql 从 Python 中使用 GraphQL API 的例子。

位于[Hugo df/python-graph QL-client-example](https://github.com/HugoDF/python-graphql-client-example)的完整代码示例。

> 这是上周一用 Hugo 简讯以[代码发出的。](https://buttondown.email/hugo)[订阅](https://buttondown.email/hugo)，在你的收件箱里获得最新的帖子(比任何人都要早)。

```
$ pip install gql # You should use a virtualenv 
```

```
from gql import gql, Client
from gql.transport.requests import RequestsHTTPTransport

_transport = RequestsHTTPTransport(
    url='https://graphql-pokemon.now.sh/',
    use_json=True,
)

client = Client(
    transport=_transport,
    fetch_schema_from_transport=True,
)
query = gql("""
{
    pokemon(name: "Pikachu") {
        attacks {
            special {
                name
            }
        }
    }
}
""")

print(client.execute(query)) 
```

```
$ python fetch.py
{'pokemon': {'attacks': {'special': [{'name': 'Discharge'}, {'name': 'Thunder'}, {'name': 'Thunderbolt'}]}}} 
```

[Isis frana](https://unsplash.com/@isisf?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)