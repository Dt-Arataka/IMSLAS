# ESP32S3驱动外置ADC
## 一、外置ADC模块介绍
### 1.芯片型号：ADS8681 
> 分辨率：16 bit 
> 采样速率：1 MSPS (最大值) 
> 基准电压：内部 4.096V 
> 通道数：单通道 
> 输入阻抗：$\ge 1 M\Omega$  
> 输入接口：接线端子 或 SMA 
> 输入带宽：15 KHz  
> 输入量程 (软件可编程)：
    >>双极性量程：$\pm 2.56V$、$\pm 5.12V$、$\pm 6.144V$、$\pm 10.24V$、$\pm 12.288V$ 
    >>单极性量程：$0V \sim 5.12V$、$0V \sim 6.144V$、$0V \sim 10.24V$、$0V \sim 12.288V$
>    
> 通信与数字接口模块通信协议：SPI 
> 通信速率：66.67 MHz
### 2.引脚功能
> 1.    CS (Chip Select / $\overline{CS}$):功能: 片选信号（低电平有效）。
>       操作: ESP32 拉低此脚以启动一帧数据传输（或启动一次转换，取决于配置）。传输结束后拉高。
> 2.    SCLK (Serial Clock):功能: 时钟信号。
>       操作: 由 ESP32 产生时钟脉冲，决定数据传输的快慢。ADS8681 支持最高 66.67 MHz 的时钟 ，完全匹配 ESP32 的高速 SPI 能力。
> 3.    SDI (Serial Data In / MOSI):功能: 数据输入（Master Out Slave In）。
>       操作: ESP32 通过此引脚向 ADC 发送命令。这在您的项目中非常重要，因为您需要通过 SDI 发送指令将输入量程配置为 0 ~ 5.12V 或 0 ~ 10.24V，否则默认量程可能不适合您的信号。
> 4.    SDO0 (Serial Data Out 0 / MISO):功能: 主数据输出（Master In Slave Out）。
>       操作: ADC 转换好的 16位数字信号通过此引脚传回 ESP32。这是您读取离子谱图数据的主要通道。 
> 5.    RST (Reset):功能: 硬件复位（低电平有效）。
      操作: 在系统上电初始化时，ESP32 应短暂拉低此引脚，确保 ADC 处于已知的初始状态。
> 6.    RVS (Read Valid Signal):功能: 数据有效/忙信号。这是 ADS8681 的一大特色。
>      操作:当 RVS 为 高电平 时，表示 ADC 正在输出有效数据或处于空闲状态;当 RVS 变低电平时，通常表示转换正在进行中。
> 7.    SDO1:功能: 第二数据输出（用于 Dual SPI 模式）。
>       操作: ADS8681 支持双线并行输出数据以加倍传输速度。
## 二、测试ADS8681
### 1.引脚分配

```C++
#define ADS_RST   14  // 复位
#define ADS_CS    15  // 片选
#define ADS_MOSI  16  // SDI (数据输入)
#define ADS_MISO  17  // SDO0 (数据输出)
#define ADS_SCLK  18  // SCLK (时钟)
```
