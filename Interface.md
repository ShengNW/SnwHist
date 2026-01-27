# Interface 规则

适用于所有读取或写入本仓库的智能体。

1. 对 SnwHist 本体：项目根目录 md 是唯一事实来源；对子仓库：其项目根目录 md 是其事实来源。
2. 每次在本目录开始的会话，必须把用户 prompt 追加到 `PromptHist.md`（只记录用户 prompt）。
3. 新增或更新根目录关键 md 时，必须在 `AGENTS.md` 中添加或更新索引说明。
4. 允许更新子模块与相关 Git 元数据（如 `.gitmodules` 和子模块指针）。
5. 不得编辑任何子文件夹中的 md 文档。
6. `index.md` 由本体维护；子目录维护者在更新子目录时必须同时提交 `index.md` 变更，并通过 PR 合并。
7. 子目录维护者完成子目录更新后，必须先将子目录提交到其对应 Git 仓库，再提交并 PR 更新 `index.md`。
8. 使用 Codex 不需要阅读 `AGENTS.md`；面向使用的最小路径是 `Interface.md` + `index.md`。
9. Interface 可按场景定制探索路径；允许在任务提示中声明应执行的步骤。

## 交接提示词结构（示例，可复制调整）

前置提示：

```
对话历史存放在$HOME/SnwHist中，请阅读index.md找到与本次对话相关的交接文件
```

任务追加（示例）：

```
请参考$HOME/SnwHist/Interface.md为本次会话新建SnwHist信息生命体的独立仓库，
建好后推到git@github.com:ShengNW/SnwHist_FirstExample.git，
并更新$HOME/SnwHist的index.md和相关git子仓库关联，
最后以PR形式提交到git@github.com:ShengNW/SnwHist.git
```
