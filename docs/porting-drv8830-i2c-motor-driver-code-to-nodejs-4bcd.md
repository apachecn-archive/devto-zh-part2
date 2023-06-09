# 将 DRV8830 I2C 电机驱动程序代码移植到 NodeJS

> 原文：<https://dev.to/ladvien/porting-drv8830-i2c-motor-driver-code-to-nodejs-4bcd>

在本系列文章的前面，我展示了如何安装 NodeJS——使用安装脚本非常简单。然而，我想我最好展示一下我是如何实际使用 NodeJS 来创建我的小 1b1 驱动程序代码的。

还是那句话，简单，我用了别人的心血。特别是 Sparkfun 的 MiniMoto 图书馆的 Michael Hord。

*   [MiniMoto Arduino 库](https://github.com/sparkfun/SparkFun_MiniMoto_Arduino_Library/tree/V_1.1.0)

实际上，我所做的只是稍微调整了一下代码以适应 JavaScript 语法。

结果

```
'use strict';
var i2c = require('i2c-bus');
var sleep = require('sleep');

// Commands
const FAULT_CMD         = 0x01;

// Fault constants
const CLEAR_FAULT       = 0x80;
const FAULT             = 0x01;
const ILIMIT            = 0x10;
const OTS               = 0x08;
const UVLO              = 0x04;
const OCP               = 0x02;

// Direction bits
const FORWARD           = 0b00000010;
const REVERSE           = 0b00000001;
const HI_Z              = 0b00000000;
const BRAKE             = 0b00000011;

module.exports = class Motor {

    /** 1\. Add "inverse" motor option
     *  2\. Add option to clear fault on each motor call.
     *  
     */

    constructor(address, i2cbus, options = undefined) {        
        this.address = address
        this.i2cbus = i2cbus
        this.options = options
    }

    getFault() {

        var fault = {
            message: '',
            code: 0
        }

        var faultCode;
        try {
            this.i2cbus.readByteSync(this.address, FAULT_CMD);
        } catch (e) {
            console.log(`Read fault failed: ${e}`)
        }

        fault.code = faultCode;

        if (faultCode !== undefined) {
            console.log(faultCode);
            fault.message = 'Unknown fault.';
            switch (faultCode){
                case FAULT:
                    fault.message = 'Unknown fault.'
                    break;
                case ILIMIT:
                    fault.message = 'Extended current limit event'
                    break;
                case OTS:
                    fault.message = 'Over temperature.'
                    break;
                case UVLO:
                    fault.message = 'Undervoltage lockout.'
                    break;
                case OCP:
                    fault.message = 'Overcurrent lockout.'
                    break;
                default:
                    fault.message = 'Unknown fault.'
                    break;
            }
            return fault;
        } else {
            fault.message = 'No fault';
            return fault;
        }
    }

    clearFault() {
        var fault = this.getFault(this.address);
        if (fault.code) {
            try {
                var success = this.i2cbus.writeByteSync(this.address, FAULT_CMD, CLEAR_FAULT);
                if (success) { return true; }
            } catch (e) {
                console.log(`Failed to clear faults: ${e}`)
            }
        }
        return false;
    }

    drive(speed = 0, direction = undefined, checkFault = false) {
        // The speed should be 0-63.
        if (checkFault) { this.clearFault();}
        if (direction === undefined) {        
            direction = speed < 0;
            speed = Math.abs(speed);
            if (speed > 63) { speed = 63; }
            speed = speed << 2 ;
            if (direction) { speed |= FORWARD; }
            else           { speed |= REVERSE; }
        } else {
            speed = speed << 2 ;
            speed |= direction;
        }
        try {
            this.i2cbus.writeByteSync(this.address, 0x00, speed);
        } catch (e){
            console.log('Drive command failed.')
        }
    }

    brake() {
        try {
            this.drive(0, HI_Z);
        } catch (e) {
            console.log('Brake command failed.')
        }
    }

    stop() {
        try {
            this.drive(0, BRAKE);
        } catch (e) {
            console.log('Brake command failed.')
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

还有很多工作要做，但它是有效的。

全部列表:

1.  让构造函数接受一个`options`对象
2.  添加`read()`获得电机设定的当前速度。
3.  重构选项以清除写入时的错误，将在构造期间确定
4.  增加加速和减速算法增加功能。
5.  创建故障代码的异步轮询。

但是！就目前而言，它是有效的。

还有，或者那些像“你偷了代码，杜德！不酷。”Mhord 的代码有一个 [beerware](https://en.wikipedia.org/wiki/Beerware) 许可证。我给 Sparkfun 发了这封邮件，内容是关于许可证以及我如何偿还 Sparkfun 的工作。

> 嘿，霍德先生，
> 
> 我正在将您的 DRV8830 库移植到 Node——我想确保我给出了正确的答案。
> 
> > [https://github.com/Ladvien/drv8830](https://github.com/Ladvien/drv8830)
> 
> 此外，我们打算运一些啤酒给 spark fun——关于啤酒许可证。告诉我是哪一种。
> 
> 最后，我想确保 Sparkfun 的利益。看起来 DRV8830 TinyMoto 板已经停产了。>我应该建议人们自己卷吗...或者从一艘来自中国的慢船上拿些东西？
> -托马斯
> 又名，拉德维恩

但是我没有收到回复。这就是生活