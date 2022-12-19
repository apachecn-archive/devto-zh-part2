# 使用 C++标记字符串

> 原文：<https://dev.to/lucpattyn/tokenize-string-using-c-3flj>

```
std::string input = "We, are, all, strangers, here"; // our input
char delim = ','; // our delimiter

std::istringstream ss(input);
std::string token;

std::vector<std::string> tokens;
while(std::getline(ss, token, delim)) {
    tokens.push_back(token);
}

// tokens contain the array of tokenized strings separated by delimiter 
```