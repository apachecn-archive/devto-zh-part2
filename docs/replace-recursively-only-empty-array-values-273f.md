# 仅递归替换空数组值

> 原文：<https://dev.to/medadrian/replace-recursively-only-empty-array-values-273f>

嘿各位程序员，我需要你们的帮助！(请发送帮助)

假设我有数组 A 和数组 B:

A =[
' personal _ data ' =>[A =>[' test ' =>A]]，
' family _ data ' =>[A =>[' test ' =>A]，
；

B = [
'personal_data' = >' '，
' family _ data ' =>【B =>[' test ' =>B]，
' payment _ info ' =>[]
；

我必须用比数组 A 更优先的数组 B 替换数组 A 中的数据。(需要用 B 替换 A:
如果键不存在，添加它们
如果键存在，值不同，替换值
如果键存在，值没有不同，什么都不做)

即:

A = [
'personal_data' = >' '，
' family _ data ' =>[
A =>[' test ' =>A]
B =>[' test ' =>B]
，
' payment _ info ' =>[]
；