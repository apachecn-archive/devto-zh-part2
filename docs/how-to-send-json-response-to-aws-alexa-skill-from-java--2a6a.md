# 如何从 JAVA 向 AWS Alexa 技能发送 JSON 响应

> 原文：<https://dev.to/mu/how-to-send-json-response-to-aws-alexa-skill-from-java--2a6a>

```
 JSONObject outputSpeechContent = new JSONObject();
outputSpeechContent.put("type", "PlainText");
outputSpeechContent.put("text", engineResponse.getText());
JSONObject outputSpeech = new JSONObject();
outputSpeech.put("outputSpeech", outputSpeechContent);

JSONObject resObj = new JSONObject();
resObj.put("version", "1.01");
resObj.put("response", outputSpeech);
response.setStatusCode(200); 
```

Enter fullscreen mode Exit fullscreen mode