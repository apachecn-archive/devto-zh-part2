# big commerce——创建一个 WebHook

> 原文：<https://dev.to/ijason/bigcommerce--creating-a-webhook-4j6o>

关于如何使用 BigCommerce 的 API 创建 webhook，我找不到任何可靠的 C#示例，所以我想分享一下我的解决方案。下面的示例代码将在您的 BigCommerce 商店中创建订单时创建一个 webhook。

```
//BigCommerce Authorization
string clientID = "<your_client_id>";              
string token = "<your_token>";
string storeHash = "<your_store_hash>";
string resourcePath = "hooks";

string baseURL = "https://api.bigcommerce.com/stores/" + storeHash + "/v2/" + resourcePath;

ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;

HttpWebRequest req = (HttpWebRequest)WebRequest.Create(baseURL);
req.AllowAutoRedirect = true;
req.ContentType = "application/json";
req.Accept = "application/json";
req.Method = "POST";

req.Headers.Add("X-Auth-Client", clientID);
req.Headers.Add("X-Auth-Token", token);             

//send scope and destination as json
using (var streamWriter = new StreamWriter(req.GetRequestStream()))
{
    string json = "{\"scope\":\"store/order/created\"," +
                  "\"destination\":\"https://app.example.com/orders\"}";

    streamWriter.Write(json);
    streamWriter.Flush();
    streamWriter.Close();
}

string jsonResponse = null;
using (WebResponse resp = req.GetResponse())
{
    if (req.HaveResponse && resp != null)
    {
        using (var reader = new StreamReader(resp.GetResponseStream()))
        {
            jsonResponse = reader.ReadToEnd();
        }
    }
}

Response.Write(jsonResponse); 
```