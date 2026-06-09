# system_profiler — macOS 硬件和软件信息查询工具

## 📌 简介

| 项目 | 内容 |
|------|------|
| 开发者 | Apple Inc. |
| 首次发布 | macOS 内建工具 |
| 用途 | 通过命令行查询 Mac 的硬件、网络、软件等详细配置信息 |

## 🎯 用来做什么？

- **查询硬件配置** — CPU、内存、GPU、存储、显示器等详细信息
- **导出系统报告** — 输出为 XML 或 JSON 格式，用于资产管理或故障排查
- **按类别筛选** — 只查询某类信息（如蓝牙、USB、电源），避免输出过长
- **配合脚本使用** — 自动化收集多台 Mac 的配置信息

## 🔧 常用命令

```bash
# 列出所有可用的数据类型
system_profiler -listDataTypes

# 查询硬件概览（型号、处理器、内存、序列号）
system_profiler SPHardwareDataType

# 查询软件信息（系统版本、内核、启动项）
system_profiler SPSoftwareDataType

# 查询网络配置（接口、IP、Wi-Fi）
system_profiler SPNetworkDataType

# 查询存储设备
system_profiler SPStorageDataType

# 查询电源/电池信息
system_profiler SPPowerDataType

# 查询所有信息，输出为 JSON 格式
system_profiler -json -detailLevel full

# 查询所有信息，输出为 XML 格式
system_profiler -xml -detailLevel full
```

## 💡 日常使用示例

### 1. 查看 Mac 型号和处理器信息

```bash
$ system_profiler SPHardwareDataType

Hardware:

    Hardware Overview:

      Model Name: MacBook Pro
      Model Identifier: MacBookPro18,3
      Model Number: Z14X0008CLL/A
      Chip: Apple M1 Pro
      Total Number of Cores: 8 (6 performance and 2 efficiency)
      Memory: 16 GB
      System Firmware Version: 8422.141.2
      OS Loader Version: 8422.141.2
      Serial Number (system): XXXXXXXXXXXX
      Hardware UUID: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
      Provisioning UDID: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
```

### 2. 导出电池健康报告

```bash
$ system_profiler SPPowerDataType

Power:

    AC Charger Information:
      Connected: Yes
      Charging: Yes

    Battery Information:
      Model Information:
        Serial Number: XXXXXXXXXXX
        Manufacturer: DP
        Device Name: bq20z69
      Health Information:
        Cycle Count: 128
        Condition: Normal
        Maximum Capacity: 97%
```

### 3. 导出完整系统配置为 JSON，供脚本处理

```bash
# 只查询硬件和存储信息，输出 JSON
$ system_profiler -json SPHardwareDataType SPStorageDataType | python3 -c "import sys,json; d=json.load(sys.stdin); print(json.dumps(d['SPHardwareDataType'][0]['chip_type'], indent=2))"
```

## 🚨 常见坑点

| 问题 | 可能原因 | 解决方法 |
|------|---------|----------|
| 命令执行非常慢（几十秒） | `-detailLevel full` 查询所有数据 | 指定具体数据类型，如 `system_profiler SPHardwareDataType` |
| 输出太长刷屏 | 未加类型筛选 | 先用 `-listDataTypes` 查看可用类型，只选需要的 |
| JSON 输出是数组嵌套结构 | `-json` 会把每个类型放在数组里 | 用 `jq` 或 Python 提取具体字段 |
| 某些信息需要 root 权限 | 部分硬件诊断数据受限 | 加 `sudo` 重试（如 SPPowerDataType 中的电池序列号） |

## 🔗 参考链接

- [Apple Developer — system_profiler 手册](https://developer.apple.com/library/archive/documentation/Darwin/Reference/ManPages/man8/system_profiler.8.html)
- 终端运行 `man system_profiler`
