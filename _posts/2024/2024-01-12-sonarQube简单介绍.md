---
layout: mypost
title: sonarQube简单介绍
tags:
  - 代码质量
---

## 什么是 sonarQube

**SonarQube** 是一个开源平台，由 SonarSource 开发，专门用于持续检查代码质量。它通过对代码进行静态分析来自动执行审查，以检测多种编程语言上的错误和代码异味。以下是关于 SonarQube 的一些关键点：

1. **代码质量检查**：SonarQube 可以检测代码中的错误、代码异味和潜在的安全问题。
2. **支持多种编程语言**：SonarQube 支持 29 种编程语言，包括 Java、C#、C++、JavaScript、Python 等。
3. **详细报告**：SonarQube 提供有关重复代码、编码标准、单元测试、代码覆盖率、代码复杂性、注释、错误和安全建议的报告。
4. **集成与扩展性**：SonarQube 可以与 Maven、Ant、Gradle、MSBuild 和多种持续集成工具（如 Atlassian Bamboo、Jenkins、Hudson 等）集成。此外，还可以通过插件进行扩展，例如 SonarLint 插件可以与多种开发环境集成。

## 为什么使用 sonarQube

一个项目的生成,维护,重构这一整个过程中,可能会经历不同的开发者,如果项目管理不当,就会导致出现代码冗余多,代码逻辑重复率高以及隐藏各种各样离谱 bug 的情况.而 sonarQube 就正好能够解决这方面的问题.

1. **持续的代码质量监控**：SonarQube 允许团队在开发过程中持续监控代码质量，确保代码的健康状态。
2. **提前发现问题**：通过静态代码分析，SonarQube 可以在代码合并到主分支之前发现和修复潜在的问题。
3. **代码健康度仪表板**：SonarQube 提供了一个直观的仪表板，显示代码的健康状况、技术债务和其他关键指标。
4. **提高代码质量**：SonarQube 的目标是帮助开发人员写出更好、更安全的代码，从而提高整体的代码质量。
5. **集成与自动化**：SonarQube 可以轻松集成到 CI/CD 流程中，使代码质量检查成为自动化的一部分。

总的来说，使用 SonarQube 可以帮助团队维护高质量的代码，减少错误和安全风险，并提高开发效率。

## 怎么用 sonarQube

1、安装 sonarQube

- 官网下载工具:www.sonarqube.org/
- 配置 java 环境后
- 根据对应系统进入到不同目录下，启动 `StartSonar.bat`
- 浏览器访问 http://localhost:9000，输入 admin/admin 可登陆
- (可以安装汉化插件)

2、安装 `sonar-scanner`

- 官网渠道下载 https://docs.sonarsource.com/sonarqube/latest/analyzing-source-code/scanners/sonarscanner/
- 配置环境变量
- 运行终端指令 `sonar-scanner -version` 测试

3、结合前端项目

- 启动 sonarQube，然后创建一个项目
- 填写信息,请记住项目标识
- 创建令牌
- 在项目根目录下创建一个 `sonar-project.properties` 文件,并进行基本配置
- 最后,在项目下执行 sonar-scanner 命令，就可以在启动的 sonarQube 的浏览器页面上看到相关的代码质量分析

> 参考文章：https://juejin.cn/post/6844903910436503559

## sonarlint + vscode

**SonarLint** 是一个高级的代码检查工具，旨在帮助开发者在 IDE 中实现清晰的代码。以下是关于 SonarLint 的详细介绍以及如何在 VSCode 中使用和配置它：

1、 **什么是 SonarLint？**

SonarLint 是一个在 IDE 中的高级代码检查工具，它可以帮助你提高编码水平并及早发现问题。SonarLint 的功能类似于拼写检查器，它可以实时标记代码中的问题，并在你编码时进行实时分析，以检测常见的错误、复杂的 bug 和热点。此外，SonarLint 是一个免费的 IDE 插件，可以从你的 IDE 市场中安装。

2、 **SonarLint 的特点：**

- **广泛的规则覆盖**：SonarLint 提供了 5000 多条规则，涵盖了各种问题，包括 bug、代码异味、漏洞以及热点，并支持最新的语言标准。这些规则涵盖了所有与代码质量相关的属性，如可靠性、可维护性、可读性、安全性等。
- **实时分析、指导和快速修复**：SonarLint 在你编码时提供即时反馈。它不仅仅是一个代码检查工具，还可以突出显示编码缺陷，并解释为什么这个问题是有害的以及如何修复它。"Quick fixes"智能地为你的特定代码提供解决方案，因此你可以实时自动修复被标记的问题。
- **统一的团队规则和分析设置**：确保在开发和生产过程中的每个阶段都有覆盖范围，从 IDE 到 CI/CD 再到 IDE。当与 SonarQube 或 SonarCloud"连接"时，规则和分析设置会同步到 SonarLint，使团队围绕一个清晰的代码标准达成一致。

3、 **如何在 VSCode 中使用和配置 SonarLint？**

1. 打开 VSCode 的扩展市场。
2. 搜索并安装 SonarLint 插件。
3. 安装完成后，SonarLint 将自动为你的代码提供实时分析。
4. 你可以进一步配置 SonarLint，例如连接到 SonarQube 或 SonarCloud，以同步团队的规则和分析设置。

如果你需要更多的技术细节或配置指南，可以查看[SonarLint 的官方文档](https://www.sonarsource.com/products/sonarlint/)。

> 参考文章：https://juejin.cn/post/6971437881977995294

## 后记

上面的案例 demo 是使用的 windows 系统进行搭建的。正常来说应该使用 Docker 搭建 SonarQube 环境。

具体参考官方文档：https://docs.sonarsource.com/sonarqube/9.9/try-out-sonarqube/
