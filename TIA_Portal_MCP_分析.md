# heilingbrunner/tiaportal-mcp 仓库分析报告

## 项目概述

**heilingbrunner/tiaportal-mcp** 是一个Visual Studio Code扩展和MCP（Model Context Protocol，模型上下文协议）服务器，用于连接西门子TIA Portal与现代开发工具（如VS Code和GitHub Copilot）。该项目使自动化工程师和程序员能够直接从VS Code中连接到TIA Portal实例，与TIA Portal项目交互，并执行基本的工程操作。

### 项目地址
- GitHub仓库: https://github.com/heilingbrunner/tiaportal-mcp
- VS Code市场: https://marketplace.visualstudio.com/items?itemName=JHeilingbrunner.vscode-tiaportal-mcp

## 系统要求

- **.NET Framework 4.8**
- **西门子TIA Portal V20**（或更新版本；早期版本需要特殊标志）
- **TIA Portal Openness包**（随STEP 7/WinCC提供）
- **Windows用户组成员资格**：用户必须是"Siemens TIA Openness"组的成员
- **环境变量**：设置 `TiaPortalLocation` 为TIA Portal安装路径（例如：`C:\Program Files\Siemens\Automation\Portal V20`）

## 实现的TIA Portal Openness接口方法

该项目通过TIA Portal Openness API实现了以下核心方法：

### 1. 连接和项目管理

#### ConnectToTiaPortal
- **功能**：启动或附加到正在运行的TIA Portal实例
- **用途**：建立与TIA Portal的连接

#### OpenProject
- **功能**：打开TIA Portal项目
- **用途**：加载指定的项目文件以进行操作

#### CloseProject
- **功能**：关闭当前打开的TIA Portal项目
- **用途**：安全关闭项目并释放资源

### 2. 块和类型操作

#### ExportBlock
- **功能**：从TIA Portal项目导出特定块
- **支持的块类型**：
  - OB（组织块）
  - FB（功能块）
  - FC（功能）
  - GlobalDB（全局数据块）
  - InstanceDB（实例数据块）
  - PlcStruct（PLC结构）
- **导出格式**：.s7dcl 或 .s7res 文档
- **要求**：
  - 需要完全限定的 `blockPath`（例如：Group/Subgroup/Name）
  - 块必须是一致的（已编译）才能导出
  - 不一致的块将不会被导出

#### ImportBlock
- **功能**：导入外部源文件并在TIA Portal项目中生成块
- **支持的格式**：.s7dcl 或 .s7res（仅TIA Portal V20+）
- **支持的语言**：SCL、STL、LAD等
- **限制**：
  - 从SD文档导入LAD块需要源文件包含en-US语言标签
  - 仅适用于TIA Portal V20或更新版本

#### ExportType
- **功能**：导出PLC数据类型
- **用途**：导出用户定义的数据类型以进行备份或迁移

#### ImportType
- **功能**：导入PLC数据类型
- **用途**：从外部文件导入数据类型定义

### 3. 编译功能

#### CompilePLC
- **功能**：编译指定的PLC软件
- **用途**：
  - 在导出之前验证块的一致性
  - 在导入后编译新块
  - 在部署之前验证代码
- **重要性**：编译状态对于成功导出和避免不一致块相关的错误至关重要

### 4. 项目结构和导航

#### GetProjectStructure
- **功能**：获取TIA Portal项目结构
- **输出格式**：Markdown格式
- **用途**：
  - 项目文档生成
  - 结构分析
  - 项目比较

#### BrowseProject
- **功能**：浏览TIA Portal项目的内容
- **用途**：导航项目层次结构，查看可用的块和类型

### 5. 批量操作

#### BulkExport
- **功能**：批量导出多个块或类型
- **特点**：
  - 跳过不一致的块
  - 返回导出失败的块的详细信息
  - 仅导出一致的块和类型

#### BulkImport
- **功能**：批量导入多个块或类型
- **要求**：TIA Portal V20或更新版本

## 提供的MCP工具

该项目通过MCP服务器提供以下工具，可以被AI助手（如Claude和GitHub Copilot）调用：

### 1. 连接工具
- **connect**：连接到TIA Portal实例
- **disconnect**：断开与TIA Portal的连接

### 2. 项目管理工具
- **open_project**：打开TIA Portal项目
- **close_project**：关闭当前项目
- **get_project_info**：获取项目信息和结构

### 3. 块操作工具
- **export_block**：导出单个PLC块
- **import_block**：导入单个PLC块
- **export_all_blocks**：导出所有PLC块
- **list_blocks**：列出项目中的所有块

### 4. 类型操作工具
- **export_type**：导出PLC数据类型
- **import_type**：导入PLC数据类型
- **export_all_types**：导出所有数据类型
- **list_types**：列出项目中的所有类型

### 5. 编译工具
- **compile_plc**：编译PLC软件
- **compile_block**：编译单个块
- **check_consistency**：检查块的一致性

### 6. 文档和查询工具
- **get_structure_markdown**：以Markdown格式获取项目结构
- **search_blocks**：搜索特定的块
- **get_block_info**：获取块的详细信息

## 主要功能特点

### 1. AI代理集成
- 与GitHub Copilot集成
- 与Claude Desktop集成
- 支持对话式自动化
- 允许通过自然语言命令执行工程任务

### 2. 自动化工作流
- 自动化重复性工程任务
- 批量导出/导入操作
- 自动编译和验证
- CI/CD集成支持

### 3. 错误处理
- 丰富的错误信息
- 标准化的异常映射
- 详细的失败原因报告
- 路径建议功能（当块路径不正确时）

### 4. 版本兼容性
- 默认支持TIA Portal V20
- 可配置支持早期版本
- 版本特定功能的条件支持

## 使用场景

### 1. 代码管理
- 将PLC代码导出到Git仓库
- 版本控制TIA Portal项目
- 代码审查和协作

### 2. 自动化测试
- 自动编译验证
- 批量一致性检查
- 集成到CI/CD流水线

### 3. 文档生成
- 自动生成项目结构文档
- 块和类型清单
- Markdown格式的技术文档

### 4. 代码生成
- AI辅助的PLC代码生成
- 基于模板的块创建
- 自动化的代码标准化

### 5. 项目迁移和备份
- 批量导出项目组件
- 项目备份自动化
- 跨项目的块迁移

## 已知限制

1. **LAD块导入**：从SD文档导入梯形图(LAD)块时，所有项目必须标记为'en-US'，否则导入可能失败（这是TIA Portal Openness的已知问题）

2. **路径要求**：ExportBlock必须使用完整的块路径（例如：`Group/Subgroup/Name`），否则服务器将返回可能的完整路径建议

3. **一致性要求**：不一致的块/类型永远不会被导出；用户必须先编译

4. **版本限制**：批量导出/导入功能仅适用于TIA Portal V20或更新版本

5. **格式限制**：某些导入/导出功能需要特定的文件格式和语言标签

## 配置示例

### VS Code配置（.vscode/mcp.json）
```json
{
  "mcpServers": {
    "tiaportal": {
      "command": "path/to/TiaMcpServer.exe",
      "args": [],
      "env": {
        "TiaPortalLocation": "C:\\Program Files\\Siemens\\Automation\\Portal V20"
      }
    }
  }
}
```

### Claude Desktop配置
```json
{
  "mcpServers": {
    "tiaportal": {
      "command": "path/to/TiaMcpServer.exe",
      "args": ["--version", "20"]
    }
  }
}
```

## 工作流程示例

### 典型操作序列：
1. 从VS Code连接到TIA Portal（使用MCP服务器）
2. 在Copilot中使用代理模式选择工具
3. 使用ExportBlock导出所有块/类型或选定的块/类型
4. 使用ImportBlock为新块导入外部源
5. 使用CompilePLC编译PLC软件以进行验证
6. 处理错误和不一致性；遵循服务器/代理关于错误响应的指导

## 技术架构

### 核心组件：
1. **TiaMcpServer.exe**：MCP服务器可执行文件
2. **VS Code扩展**：提供VS Code集成
3. **TIA Portal Openness接口**：.NET基础API
4. **错误处理层**：标准化的异常映射
5. **MCP协议实现**：与AI代理的通信

### 实现语言：
- **C#**：核心服务器实现
- **.NET Framework 4.8**：运行时环境
- **TypeScript**：VS Code扩展

## 社区和贡献

- 活跃的GitHub讨论区
- 鼓励测试驱动开发
- 欢迎扩展、错误修复和CI/CD集成贡献
- 详细的错误模型文档（error-model.md）
- 示例和配置模板

## 参考资源

1. **官方文档**：
   - TIA Portal Openness介绍和演示应用
   - TIA Portal Openness自动化工程工作流
   - Openness API自动化文档

2. **社区资源**：
   - GitHub讨论：用例列表
   - GitHub讨论：单元测试
   - LobeHub MCP服务器列表

3. **技术支持**：
   - 西门子工业支持：导入外部源文件
   - 西门子技术文档
   - VS Code市场页面

## 总结

heilingbrunner/tiaportal-mcp是一个强大的桥梁工具，它将西门子TIA Portal与现代开发环境和AI工具连接起来。通过实现TIA Portal Openness API的关键方法，并将其封装为MCP工具，该项目使工业自动化工程师能够：

- 使用熟悉的开发工具（VS Code）
- 利用AI辅助进行编程和文档编写
- 自动化重复性工程任务
- 将PLC代码集成到现代CI/CD流程中
- 提高开发效率和代码质量

这个项目特别适合需要在TIA Portal和现代软件开发实践之间架起桥梁的自动化专业人员。
