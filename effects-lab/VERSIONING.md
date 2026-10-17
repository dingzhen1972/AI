# 迭代与回滚

Git 不会因为“做一次迭代”自动创建分支。团队需要执行分支流程；提交（commit）记录可恢复的历史，分支用于隔离开发。

## 本次版本

- 仓库：dingzhen1972/AI
- v1 基准提交：`0bc898b1681fbb610c2d24a4928df2fcd8a06c81`
- v2 开发分支：`feat/effects-lab-v2`
- 目标分支：`main`
- v2 功能：一键预设、随机灵感、在当前浏览器保存和恢复参数。
- 本次上传并创建 PR，不自动合并。main 在 PR 合并前继续保持旧版。

## 日常操作

第一次在电脑下载仓库：

```sh
git clone https://github.com/dingzhen1972/AI.git
cd AI
```

查看本次新版（工作区应先提交或暂存未完成的修改）：

```sh
git fetch origin
git switch --track origin/feat/effects-lab-v2
```

如果本地已经有这个分支，使用 `git switch feat/effects-lab-v2`。打开 `effects-lab/index.html` 即可体验。

下一次迭代示例（v2 合并后进行）：

```sh
git switch main
git pull --ff-only origin main
git switch -c feat/effects-lab-v3
# 修改文件并完成检查
git add effects-lab
git commit -m "Improve effects lab v3"
git push -u origin feat/effects-lab-v3
```

在 GitHub 点击 Compare & pull request，以 main 为目标分支。检查 Files changed，确认后选择 Create a merge commit 合并，方便以整个 PR 为单位撤销。

## 尚未合并时

main 没有变化，无需回滚。想放弃新版，可关闭 PR；想继续修改，继续提交到相同分支，PR 会同步更新。切回 main 只是更换本地查看的版本，不会撤销已经合并的改动。

## 合并后撤销整个迭代

推荐 GitHub 网页操作：打开已合并的 PR → Revert → 创建回滚 PR → 检查差异 → 合并回滚 PR。GitHub 会增加反向提交，保留历史。如果发生冲突或无法自动回滚，使用命令行处理。

命令行方式（适用于使用 Create a merge commit 合并的 PR）：

```sh
git switch main
git pull --ff-only origin main
git switch -c revert/effects-lab-v2
git log --oneline --merges -n 10
# 将下面 MERGE_COMMIT_SHA 替换成该 PR 的合并提交 SHA
git revert -m 1 MERGE_COMMIT_SHA
git push -u origin revert/effects-lab-v2
```

然后从回滚分支向 main 创建并合并 PR。MERGE_COMMIT_SHA 指的是合并提交，不是 v1 的基准提交。若采用 Squash and merge，撤销对应压缩提交时用 `git revert SQUASH_COMMIT_SHA`，不加 `-m 1`。冲突需检查解决后 `git add`、`git revert --continue`；放弃本次回滚可用 `git revert --abort`。

无需使用 reset --hard 或强制推送来撤销共享分支的正常迭代。回滚可能与后续修改冲突，合并前应检查和验证。

## 只想临时查看原始 v1

```sh
git switch --detach 0bc898b1681fbb610c2d24a4928df2fcd8a06c81
# 查看完后返回
git switch main
```

此操作不会更改 GitHub 上的 main。如果还有在线部署，代码回滚后还需要相应的部署；本项目目前只上传源码。

官方说明：https://docs.github.com/en/pull-requests/how-tos/merge-and-close-pull-requests/reverting-a-pull-request