# PowerShell 编辑注册表到触发器滚轮(鼠标自然滚动)

> 原文：<https://dev.to/cfleischhacker/powershell-edit-registry-to-flipflopwheel-mouse-natural-scrolling-4fij>

# 用触发器滚轮查看所有注册表项

get-item property HKLM:\ SYSTEM \ current control set \ Enum \ HID * * \ device ` Parameters flip flowheel-EA 0

# 使用触发器滚轮更改每个条目的注册表设置

# 反转鼠标滚轮滚动 FlipFlopWheel = 1

# 正常鼠标滚轮滚动触发器滚轮= 0

get-item property HKLM:\ SYSTEM \ current control Set \ Enum \ HID * * \ device ` Parameters flipflowheel-EA 0 | ForEach-Object { Set-item property $ _。PSPath 触发器滚轮 1 }