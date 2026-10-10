# STM32F103 ADC Acquisition Test

这是声振实验前期做的 STM32F103C8 单通道采集测试：定时触发 ADC、用循环 DMA 缓冲，再通过串口把原始采样值送到上位机。它验证的是采集链路，不是完整仪器固件。

*An early STM32F103C8 acquisition test using timer-triggered ADC sampling, circular DMA buffering, and serial transfer.*

## 数据链路

`PA0 模拟输入 → ADC1 → DMA1 循环缓冲 → 半缓冲/满缓冲事件 → USART → 上位机`

- `Hardware/AD.c`：TIM3 触发 ADC1；代码配置的目标采样率为 **5 kHz**。DMA 缓冲区共 2048 个 `uint16_t` 采样值。
- `User/main.c`：按键切换采样状态，在每个 1024 点半缓冲区就绪后发送数据帧。
- `Hardware/Serial.c`：串口发送逻辑。上位机实验分析代码见 [Liquid Sloshing Signal Analysis](https://github.com/Supercraft-1/Liquor_Sloshing_Signal_Analysis)。

## 工程入口

使用仓库根目录的 `Project.uvprojx` 在 Keil µVision 中查看或构建工程。编译、烧录和接线前，请核对实际 MCU、时钟、ADC 输入引脚、串口以及配套采集设备。此 README 更新未进行实体硬件复测。

## 原型边界

当前串口接收流程依赖固定的 **1024 点数据帧**；帧头中的长度字段只有 1 字节，不能完整表示 1024，若要扩展为通用可变长协议，需要一并修改发送端与接收端。此仓库展示采集链路，不包含经验证的液体识别模型或检测准确率。
