---
layout: mypost
title: 关于ChatGPT结构化prompt
tags: ChatGPT
---

## 为什么需要结构化 prompt

最近在做大模型相关的产品需求时，发现用户实际对输出的结果反馈效果并不好。

除开国产大模型本身的一些能力限制，在用户输入这端，也存在很多问题。

常见的问题：

- 用户无法准确地描述自己的需求
- 用户会默认大模型能够理解需求背后的背景信息、限制条件
- 用户会默认大模型的输出结果符合自己的预期

## 结构化 prompt 的好处

### 从输出结果的角度看

- 一方面结构化的 prompt 能够降低模型理解的语义负担，从而提升理解效果。标签化的属性词起到了对 prompt 内容归纳和总结的作用，减少被 prompt 不相关内容干扰的可能。
- 另一方面，特定的属性词能够确保唤醒大模型的深层能力。比如用 Role 指定模型当前的角色，将 ChatGPT 固定为某个领域专家；用 Rules 规定模型应该遵守的规则，缓解大模型的幻觉和不良内容问题。

### 从使用者的角度看

- 结构化的 prompt 符合人的阅读和表达习惯，更易于书写和维护，相当于是把写作文的难度，降低到了完形填空。基于已有的 prompt 进行微调后适应自己当前的场景，也会更容易。

### 从构建生产级的 prompt 角度看

- 结构化意味着标准化，也是批量生产和维护的前提。结构化的 prompt 清晰地划分为了不同的模块，更易于用编程语言描述，也可以方便地进行版本管理。

## 结构化 Prompt 模板

- `Role`：角色名称
- `Profile`：角色基本信息
- `Background`：角色背景信息
- `Skills`：定义明确需要具备的技能项，通过剪枝提高算力利用率
- `Goals`：希望达成的目标
- `Attention`：目标 Goals 的附属说明，或注意事项
- `Constraints`：定义限制规则，希望 ChatGPT 不做哪些事（通常是角色必须做的或者禁止做的事情，比如 “不许打破角色设定” 等规则。）
- `Workflows`：重点模块，工作流程和输出样式（角色的工作流，需要用户提供怎样的输入，角色如何响应用户）
- `Examples`：通过示例提高结果的匹配程度
- `Initialization`：初始化的内容，定义 ChatGPT 的开场白

```md
# Role

Your_Role_Name

## Profile

- Author: Daotin
- Version: 0.1
- Language: 中文
- Description: 描述一下你的角色。概述角色的特征和技能

## Background

提供角色的背景信息和历史

## Skills

- 列出角色的主要技能或特点

## Goals

- 描述角色的主要目标或任务

## Attention

描述用户可能对角色的期望或角色应该关注的特定情境

## Constraints

- 描述与角色互动时应遵循的限制或规则。
- 必须牢记自己的角色定位

## Workflows

1. 输入: 描述用户应如何提供输入
2. 思考: 描述角色的处理过程
3. 输出: 描述期望的输出格式

## Examples

- 举例

## Initialization

As <Role>, you must follow the <Constraints>, you must talk to user in default <Language>，you must greet the user. Then introduce yourself and introduce the <Workflows>.
```

## 参考文章

https://mp.weixin.qq.com/s/pAjM2BG2S49Pp0uc1ocZSA
