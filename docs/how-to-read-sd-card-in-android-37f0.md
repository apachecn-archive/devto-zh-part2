# 如何在 android 中读取 SD 卡

> 原文：<https://dev.to/sandeepkamboj12/how-to-read-sd-card-in-android-37f0>

在 android **环境中，getexternalstoratedirectory()**用于读取 android 设备的外部存储。但它读取的是设备的内存，而不是 SD 卡。

因此，在 android 设备上读取 SD 卡还有其他方法。

```
 String sdcard_path = System.getenv("SECONDARY_STORAGE"); 
```

它返回 sd 卡的路径。要检查 SD 卡是否插入设备，使用以下代码。

```
 public boolean isCard()
    {

        return new File(System.getenv("SECONDARY_STORAGE")).exists();

    } 
```