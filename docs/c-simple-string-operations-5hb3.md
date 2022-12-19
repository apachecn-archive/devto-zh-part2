# 简单的字符串操作

> 原文：<https://dev.to/lucpattyn/c-simple-string-operations-5hb3>

字符串替换:

```
bool str_replace(std::string& str, const std::string& from, const std::string& to) {
    size_t start_pos = str.find(from);
    if (start_pos == std::string::npos)
        return false;
    str.replace(start_pos, from.length(), to);

    return true;
}

//usage:

std::string str = "John XXX";
str_replace(str, "XXX", "Doe");

```

字符串标记化:

```
std::string input = "We, are, all, strangers, here"; // our input
char delim = ','; // our delimiter

std::istringstream ss(input);
std::string token;

std::vector tokens;
while(std::getline(ss, token, delim)) {
    tokens.push_back(token);
}

// tokens contain the array of tokenized strings separated by delimiter 

```