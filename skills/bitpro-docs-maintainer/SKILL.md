---
name: bitpro-docs-maintainer
description: 用于维护 BitPro 文档：产品、界面、API、策略、部署或工作流变化后更新 README、docs/product/specification.md、docs/project/progress.md、docs/contracts、免责声明、截图记录和公开操作说明。
---

# BitPro 文档维护

## 概览

把 BitPro 文档当作可审计的产品合同维护，而不是杂乱的变更日志。每次文档更新都应让下一位操作者或 Codex 会话更清楚当前行为、验证状态和剩余运行风险。

## 先读这里

编辑文档前，按 `AGENTS.md` 要求读取项目状态：

1. `README.md`
2. `docs/product/specification.md`
3. `docs/project/progress.md`
4. `docs/contracts/` 下相关的 active contract
5. `git status --short`

写入前先用 `rg` 在文档中定位受影响主题。把已有未提交改动视为用户工作；不要暂存或改写无关文档片段。

## 选择正确文档面

- `docs/contracts/<主题>.md`：行为、验收标准、API 形态、UI 规则、部署边界或验证预期变化时更新。
- `docs/product/specification.md`：产品级合同变化，或当前 sprint 之外也需要可发现的行为变化时更新。
- `docs/project/progress.md`：有意义工作后追加简洁的实现和验证状态，包含实际运行命令和延期检查。
- `README.md` / `README_EN.md`：只在公开产品定位、设置方式、主要功能面、截图或操作员流程变化时更新。
- `docs/product/disclaimer.md`：只在执行、信号、真实账户风险、AI 或合规口径变化时更新。
- `docs/screenshots/`：截图必须反映真实部署行为。不要为了 README 效果伪造状态。

如果代码变更影响生产 SQLite 中可见策略，必须记录生产 seed 同步是否完成。若未完成，写明待执行命令，不要暗示操作者已经能在服务器看到变化。

## 写作规则

- 保持事实性、当前时态，并落在实现细节上。
- 优先写一段精准说明，而不是泛泛的功能长文。
- 路由、API、表、配置键、命令和文件重要时要写准。
- 保持 BitPro 研究优先、默认模拟盘的边界。
- 不要把 mock、synthetic 或 fallback 市场数据描述为可接受方案。BitPro 优先使用真实数据和显式错误。
- 未在本会话实际完成部署、服务器验证、SQLite 同步、截图刷新或测试覆盖时，不要声称已完成。
- 记录时效状态时使用具体日期。
- 策略名称保持规范：现货使用 `[现货]`，合约、永续、衍生品使用 `[合约]`。
- 项目文档正文默认中文；`docs/` 根目录只保留 `index.md`，通用文档按主题放入英文目录，并使用小写 kebab-case 英文文件名。顶层文档数据 JSON 也应进入分类目录并使用小写 kebab-case 英文文件名。历史页面文档、合同和研究材料可在专项迁移前保留既有中文文件名。

## 维护流程

1. 确认变化的行为和操作者可见结果。
2. 找到现有文档归属：先合同，再 `docs/product/specification.md`，公开说明再到 README。
3. 更新最小且足够的文档集合。
4. 在 `docs/project/progress.md` 添加条目，包含：
   - 改了什么；
   - 更新了哪些文档、代码或测试；
   - 实际验证命令；
   - 相关生产或部署状态。
5. 按变化类型运行验证：
   - 纯文档：`git diff --check`
   - 和代码绑定的 Markdown 或合同：相关静态测试加 `git diff --check`
   - 产品行为变化：相关测试、前端 build/lint 或后端编译，再跑 `./scripts/check.sh`
6. 暂存前查看 `git diff`，只暂存本次意图内的文档或代码片段。

## 常见问题

- 在多个位置用略有差异的措辞重复同一要求。
- 只追加进度记录，却不更新 active contract 或产品规格。
- 在 main 分支部署确认前更新 README 截图或对外声明。
- 让本地 SQLite、seed、截图或浏览器状态冒充生产状态。
- 因为 `docs/product/specification.md` 或 `docs/project/progress.md` 已有用户改动而暂存无关脏文档。

## 好的进度条目形态

```markdown
- **短标题（YYYY-MM-DD）**：说明用户可见变化和实现边界。同步更新 `docs/product/specification.md` 与 `docs/contracts/迭代示例合同01.md`，新增/更新测试覆盖关键防回退规则。验证通过：`pytest -q ...`、`npm --prefix frontend run build`、`git diff --check`、`./scripts/check.sh`。生产状态：GitHub Actions main run `<id>` 成功；服务器静态资源/SQLite 已复核。
```

如果验证不完整，把 `验证通过` 改成 `待验证` 或 `未验证`，并清楚说明阻塞原因。
