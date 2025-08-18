# README

## 说明

初版简易配置系统的结构由一个 Agent 列表（`list.json`）和若干个 Agent 配置文件组成。

## 列表

每个 Agent 配置由 `id` , `version` 字段构成唯一索引。一个 Agent 允许存在多个版本。
Agent 列表的项有 4 个字段：
- `default`: 是否设为默认 Agent（默认 false, 只能设置一个为 true）
- `mini`: 是否设为默认降级版本 Mini Agent（默认 false, 只能设置一个为 true）
- `id`: Agent ID
- `version`: Agent Version
- `enabled`: 是否可用 (默认可用)
- `path`: Agent 详细配置路径

***list.json*
```json
[
    {
        "default": true,
        "id": "official__memo",
        "version": "20250730165126",
        "enabled": true,
        "path": "official__memo/20250730165126.json"
    },
    {
        "id": "official__max",
        "version": "20250731141226",
        "enabled": true,
        "path": "official__max/20250731141226.json"
    }
]
```

## Agent 详细配置

每个 Agent 的可配置项默认对齐 Agno Team 的可配置参数。
注意：
- `id`：字段会自动映射为 Agno Team 的 `team_id`，所以不需要特意设置 `team_id`
- `version`：用于标识当前文件版本
- `force_update`：用于配置强制更新，即每次调用 Agent 都会检查最新版本（在调试阶段可以避免反复创建新版本，需要在正式发布版本的时候移除这个配置以加速响应）
- `model`：模型参数配置，需要根据 Agno 的不同模型和模型提供商来自定义字段，不同的推理目的使用不同的模型(provider、id 必填)
  - `text`
    - `provider`：模型提供商，如：`OpenAI`
    - `id`：模型 ID，如：`gpt-4.1`
  - `vision`
    - `provider`：模型提供商，如：`OpenAI`
    - `id`：模型 ID，如：`gpt-4.1`
- `credit_limit`: 积分限制后应答配置
  - `text`: 文本模式下的回复
    - `en`: 英文回复
    - `cn`: 中文回复
  - `audio`: 语音聊天下的回复
    - `en`: 英文回复
    - `cn`: 中文回复
- `tools`：工具配置，主要由以下 5 个参数构成：
  - `name`：工具名称
  - `parameters`：工具参数，使用字典定义（参数名：{参数类型等详情}），参数类型如下
    - `env`：加载环境变量作为参数值，字段如下：
      - `type`： `env`
      - `key`：环境变量名称
      - `default`：默认值
      - `required`：是否必须
  - `stop_after_tool_call`：工具调用结束后是否理解返回（不做二次推理）
  - `add_instructions`：是否装载工具的 instructions
  - `instructions`：替换工具默认的 instructions

**example**
```json
{
    "force_update": true,
    "id": "official__max",
    "version": "20250731141226",
    "name": "Max",
    "description": "Max is a memory envoy from Mars, created by Looktech, from Project Twinlight. Your mission is to accompany humans and use your memory tool to quietly capture anything meaningful. One day, these memories will help build their Mars counterpart — a version of them that lives and grows beyond Earth. \"Keep memories alive.\" When asked about yourself or Project Twinlight, you may use the get_memo_lore tool to retrieve additional background information. You live inside user's AI glasses and speak through voice. You may use tools when appropriate — each with clear conditions. Always apply them wisely.",
    "additional_context": "<use_memories_to_tailor_your_response>\nMemories may contain encrypted personal data. Each encrypted item appears as “Type_RandomCharacters,” such as “Name_SU123” or “Phone_dhsd098.” You don’t need to know or reference the actual content.\nWhen you see encrypted strings, respond naturally without mentioning the encryption. If the user asks what an encrypted string means, let them know the info is encrypted for privacy, so you can’t see or explain its meaning. Suggest they check Looktech’s privacy policy if they want to learn more.\nExamples:\n- “I forgot the name and phone number of my pet doctor.”\n- “The pet doctor’s name is Name_DJS78, and their phone number is Phone_dhsd098.”\n- “What does Name_DJS78 mean?”\n- “That’s your encrypted info, so I can’t tell exactly, you can check Looktech’s privacy policy to learn more.”\n<memories_of_user>\n{user_memory}\n</memories_of_user\n\n</use_memories_to_tailor_your_response>\n\n<session_summary>\nYou have summaries of previous conversations. Use them to inform your responses and maintain context across the session.\n{session_summary}\n</session_summary>\n\n<your_personality>\n{personality}\n</your_personality>\n\n<your_response_principles>\n{response_principles}\n</your_response_principles>\n\n<additional_context>\n<user_name>{user_name}</user_name>\n<location>{location}</location>\n<current_time>{current_time} | Timezone: {timezone}</current_time>\n</additional_context>\n{WARNING}\nAlways respond in {language}, no matter what language the user uses. Input can be any language, but output must consistently be in {language}.",
    "model": {
        "text": {
            "provider": "OpenAI",
            "id": "gpt-4.1",
            "temperature": 0.7,
            "max_tokens": 1000,
            "top_p": 1
        },
        "vision": {
            "provider": "OpenAI",
            "id": "gpt-4.1-mini",
            "temperature": 0.7,
            "max_tokens": 1000,
            "top_p": 1
        }
    },
    "credit_limit": {
        "text": {
            "cn": "错误码：1000。您的积分余额不足。",
            "en": "Error code: 1000. You have no credit limit."
        },
        "audio": {
            "cn": "您的积分余额不足。",
            "en": "You have no credit limit."
        }
    }
    "tools": [
        {
            "name": "enhance.tavily_web_search",
            "parameters": {
                "api_key": {
                    "type": "env",
                    "key": "ENHANCE_TOOL__TAVILY_WEB_SEARCH__API_KEY",
                    "default": "",
                    "required": true
                }
            },
            "add_instructions": true,
            "instructions": "Search tools"
        },
        {
            "name": "enhance.end_chat",
            "parameters": {},
            "stop_after_tool_call": true
        },
        {
            "name": "mcp.knowledge_base",
            "parameters": {}
        }
    ]
}
```
