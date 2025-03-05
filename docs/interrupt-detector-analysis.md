# Interrupt Detector 实现分析

## 概述
Interrupt Detector是一个用于检测用户打断的组件，提供了Go和Python两种实现版本。主要用于监听文本数据流，并在特定条件下触发打断机制。

## 实现版本对比

### Go版本 (interrupt_detector)
- **特点**：轻量级实现，专注于核心打断检测功能
- **主要功能**：
  - 监听文本数据
  - 发送flush命令
  - 简单的打断检测逻辑

### Python版本 (interrupt_detector_python)
- **特点**：功能更完整，提供更多控制选项
- **主要功能**：
  - 完整的生命周期管理
  - 命令处理和转发
  - 数据流转发功能
  - 打断检测逻辑

## 核心实现机制

### 打断检测条件
两个版本共同的打断触发条件：
- 文本为最终结果 (`is_final = true`)
- 文本长度达到至少2个字符 (`len(text) >= 2`)

### 数据处理流程
1. 接收文本数据，包含关键字段：
   - text: 实际文本内容
   - is_final: 是否为最终结果
2. 检查打断条件
3. 触发flush命令

## 版本特点对比

### Go版本特点
```go
func (p *interruptDetectorExtension) OnData(tenEnv ten.TenEnv, data ten.Data) {
    // 简单的检测和flush命令发送
    if final || len(text) >= 2 {
        flushCmd, _ := ten.NewCmd(cmdNameFlush)
        tenEnv.SendCmd(flushCmd, nil)
    }
}
```

### Python版本特点
```python
def on_data(self, ten: TenEnv, data: Data) -> None:
    # 更复杂的处理逻辑
    if final or len(text) >= 2:
        self.send_flush_cmd(ten)
    
    # 数据转发功能
    d = Data.create("text_data")
    d.set_property_bool(TEXT_DATA_FINAL_FIELD, final)
    d.set_property_string(TEXT_DATA_TEXT_FIELD, text)
    ten.send_data(d)
```

## 主要区别总结

| 特性 | Go版本 | Python版本 |
|------|--------|------------|
| 实现复杂度 | 简单 | 复杂 |
| 生命周期管理 | 基础 | 完整 |
| 命令处理 | 仅flush | 支持多种命令 |
| 数据转发 | 不支持 | 支持 |
| 错误处理 | 基础 | 完善 |
| 日志记录 | 基础 | 详细 |

## 使用场景

- **Go版本**：适用于只需要基本打断检测的简单场景
- **Python版本**：适用于需要更多控制和功能的复杂场景

## 总结
两个版本的实现展示了同一功能的不同实现方式，Python版本提供了更多的功能和灵活性，而Go版本则更简洁高效。选择使用哪个版本主要取决于具体的使用场景和需求。
