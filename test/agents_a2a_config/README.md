# README

## 说明

初版简易配置系统的结构由一个 Agent 列表（`list.json`）和若干个 Agent 配置文件组成。

## 列表

每个 Agent 配置由 `id` , `version` 字段构成唯一索引。一个 Agent 允许存在多个版本。
Agent 列表的项有 4 个字段：
- `default`: 是否设为默认 Agent（默认 false, 只能设置一个为 true）
- `id`: Agent ID
- `version`: Agent Version
- `enabled`: 是否可用 (**不同于 Agent LLM 配置**；默认不可用（语言问题），需要显示指定可用 true)
- `asr`: Agent ASR 详细配置
  - `hot_keywords_path`: ASR 热词列表文件路径
- `tts`: Agent TTS 详细配置
  - `provisder`: 提供商
  - `speaker`: 音色配置

***list.json*
```json
[
    {
        "default": true,
        "enabled": true,
        "id": "official__memo",
        "version": "20250730165126",
        "asr": {
            "hot_keywords_path": "official__memo/20250730165126.json"
        },
        "tts": {
            "provider": "ms",
            "speaker": "zh-CN,zh-CN-Xiaoxiao2:DragonHDFlashLatestNeural,default,+5"
        }
    },
    {
        "enabled": true,
        "id": "official__max",
        "version": "20250731141226",
        "asr": {
            "hot_keywords_path": "official__max/20250731141226.json"
        },
        "tts": {
            "provider": "ms",
            "speaker": "zh-CN,zh-CN-Yunyi:DragonHDFlashLatestNeural,default,+5"
        }
    }
]
```

## ASR 详细配置

- `hot_keywords_path`：热词列表文件路径

**example**
```json
[
    "Memo",
    "Hey, Memo",
    "Hey Memo",
    "Hi, Memo",
    "Hi Memo",
    "Looktech",
    "Entertech",
    "Flowtime"
]
```

## TTS 详细配置

同管理后台音色配置规则。

- `provisder`: 提供商
- `speaker`: 音色配置
  
**example**
```json
{
    "provider": "ms",
    "speaker": "zh-CN,zh-CN-Xiaoxiao2:DragonHDFlashLatestNeural,default,+5"
}
```
