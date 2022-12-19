# 使用 jQuery 进行字数统计

> 原文：<https://dev.to/nonny123/word-count-using-jquery-2edg>

这篇文章旨在创建一个 jQuery 字数统计函数。使用 jQuery，下面的代码可以用来限制文本框(例如研究论文报告的摘要)中的总字数。

``function WordCount() { //Get Text data var inp = document.getElementById('txtabstract').value; var regex = /\s+/gi; var wordcount = jQuery.trim(inp).replace(regex, ' ').split(' ').length; $("#labelcount").html(wordcount); } $(document).ready(function(){ //count word on page load WordCount(); //count word on entering text in TextBox $("#txtabstract").on('input',function(){ WordCount(); }) })`` `此代码用于让用户知道他的项目报告摘要何时超过了 300 字(这是摘要的标准)`