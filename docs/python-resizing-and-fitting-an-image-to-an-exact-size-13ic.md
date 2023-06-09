# Python:调整图像大小并使其适合精确的大小

> 原文：<https://dev.to/preslavrachev/python-resizing-and-fitting-an-image-to-an-exact-size-13ic>

通常，在 Python 中进行影像分析时，您会希望将影像的大小调整为统一的尺寸(通常为 2 的幂)。这里有一个简单而有效的方法来调整任意大小的图像，直到你想要的尺寸。如果新尺寸与原始比例不匹配，将从中心开始裁剪图像，以匹配新的所需比例。

```
from PIL import Image, ImageOps

original_image = Image.open("path/to/image")
size = (512, 512)
fit_and_resized_image = ImageOps.fit(original_image, size, Image.ANTIALIAS) 
```

在 StackOverlow 的帮助下