# BooMGateway AI 编程协作规范

本文件供 AI 编程助手（Claude Code / Cursor / Trae 等）读取，确保多人 AI 辅助编程风格一致。

## 提交前必须通过的检查

```bash
cargo fmt --all -- --check
cargo clippy --workspace --all-targets -- -D warnings
cargo build --workspace
cargo test --workspace
```

## 代码风格规则

### 必须遵守
- 使用 `cargo fmt` 格式化，不手动调整缩进
- 修复所有 clippy warning，不允许 `#[allow(warnings)]`
- 生产代码禁止 `unwrap()` / `expect()`，使用 `?` 或 `match` 处理错误
- 公共 API 必须有文档注释（`///`）

### clippy 修复优先级
1. **修改代码**（首选）：按 clippy 建议重构
2. **添加 allow**（最后手段）：必须指明具体 lint，如 `#[allow(clippy::too_many_arguments)]`
3. **禁止**：`#[allow(warnings)]` 这种宽泛的 allow

### 常见 clippy 修复方式
- `map_or(false, ...)` → `is_some_and(...)`
- `or_insert_with(Vec::new)` → `or_default()`
- `if let Err(_) = x` → `if x.is_err()`
- 嵌套 if → 用 `&&` 合并条件
- `.as_bytes().len()` → `.len()`

## 提交信息规范

```
<type>(<scope>): <description>

<可选的正文>

<可选的 footer>
```

type: feat / fix / ci / docs / refactor / test / chore
scope: 模块名，如 boom-routing、boom-auth、ci

示例：
- `fix(boom-routing): resolve await_holding_lock clippy warning`
- `ci: add system dependencies to GitHub Actions workflow`

## 分支策略

- `master`: 主干分支，CI 必须通过才能合入
- `dev`: 开发分支，日常集成
- 功能开发: `feat/<name>` 或 `fix/<name>`

## 禁止事项

- 禁止提交 `target/` 目录
- 禁止提交 `.env` 等含密钥的文件
- 禁止 `git push --force` 到 master/dev
- 禁止绕过 CI（除非紧急回滚）
