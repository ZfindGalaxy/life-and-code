# Git 合并冲突解决复盘 | 2026.3.20
> 记录第一次向 GitHub 推送 README.md 遇到的合并冲突问题及解决过程

## 📌 冲突背景
1. 本地操作：在 `D:/life_and_code` 文件夹初始化 Git 仓库，编写了个人简介 `README.md`；
2. 远程操作：GitHub 创建 `life-and-code` 仓库时，勾选了「Add a README file」，自动生成了默认的 `README.md` 文件；
3. 冲突触发：执行 `git push -u origin main` 时，Git 提示「远程有本地没有的内容，需先 pull」；
4. 二次冲突：执行 `git pull origin main --allow-unrelated-histories` 后，`README.md` 出现合并冲突，仓库进入 `MERGING` 状态，无法执行 pull/push。

## 📝 尝试解决（失败的思路）
- 尝试删除本地 README 中冲突标记（`<<<<<<< HEAD`、`=======`、`>>>>>>> origin/main`），重新推送，失败；
- 思考过直接修改本地文件名规避冲突，但考虑到 GitHub 会将 `README.md` 作为仓库主页，属于「治标不治本」，遂放弃该方案。

## 🛠️ 最终解决思路（清空合并→重新填充）
### 步骤 1：临时清空冲突文件，完成合并
- 打开本地 `README.md`，删除自己写的所有内容（使文件内容与远程默认 README 一致）；
- 执行 `git add README.md` 标记文件冲突已解决；
- 执行 `git commit -m "临时合并：清空README内容，结束MERGING状态"` 完成合并，退出 `MERGING` 状态。

### 步骤 2：重新填充内容并推送
- 打开 `README.md`，重新粘贴自己写的个人简介内容；
- 执行 `git add README.md`；
- 执行 `git commit -m "恢复README内容：完善个人简介"`；
- 执行 `git push -u origin main`，推送成功。

## 📚 关键知识点总结
1. 冲突本质：本地仓库和远程仓库有「无共同历史的同名文件」，Git 为避免数据覆盖，拒绝直接推送；
2. `--allow-unrelated-histories`：解决「本地/远程仓库无共同提交历史」的合并问题，是跨仓库合并的关键参数；
3. `MERGING` 状态：仓库处于合并中且冲突未解决时，禁止执行 pull/push，必须先解决冲突并提交；
4. 核心逻辑：Git 遵循「先拉取远程内容合并到本地，再推送本地内容」的规则，保障数据不丢失。

## ✨ 踩坑感悟
1. 创建 GitHub 仓库时，若本地已准备好 README，建议不勾选「Add a README file」，从源头避免冲突；
2. 遇到冲突不用慌，Git 的提示信息（如 `fix them up in the work tree`）会给出明确的解决方向；
3. 新手可先用「清空内容合并→重新填充」的“笨方法”解决问题，虽绕路但成功率高，比死磕命令行合并更高效；
4. 本次操作中两次 commit 的 `-m` 备注写得相同，是因为专注于尝试解决方法，忽略了备注的区分度——后续提交应让备注清晰体现操作目的，方便后续回溯；
5. 命令行合并的方式理论可行，但新手易因冲突标记清理不彻底导致失败，“笨方法”反而更适配新手场景。