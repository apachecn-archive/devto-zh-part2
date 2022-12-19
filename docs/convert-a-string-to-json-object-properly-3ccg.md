# 正确地将字符串转换为 json 对象

> 原文：<https://dev.to/lucpattyn/convert-a-string-to-json-object-properly-3ccg>

当 nlohmann([https://github.com/nlohmann/json](https://github.com/nlohmann/json))将 std::string 或 const char* string 正确地转换为 json 对象时，我们中的一些人遇到了问题。
下面是我是如何做到的:

```
#include <string>
#include <iostream>>
#include <sstream>

#include "json.hpp"
using namespace nlohmann;

json toJson(const char* jsonString){
    json jsonObj;
    std::stringstream(jsonString) >> jsonObj;

    return jsonObj;

}

```