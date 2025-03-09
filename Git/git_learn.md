# Git教程（以github为例）

## Git与本机的ssh连接流程
### 生成公钥和私钥

```shell
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

```

### 添加ssh公钥到代理(如果设置了公钥的密码)

```shell
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa

```

### 将ssh公钥添加到git服务器

```shell
# 查看公钥
vi ~/.ssh/id_rsa.pub

```

复制后进入github中  https://github.com/settings/keys 这个界面 点击New SSH Key 然后进入将公钥粘贴  生成即可！

### 测试连接

```shell
ssh -T git@github.com
```

## 撤销git commit 和 git add .操作

```shell
git reset HEAD~1

```
## 如果最近是第一次push
```shell
git push -set-upstream origin my_branch
```
## git仓库如何嵌入其他仓库(使用git子模块)

### 首先初始化自己的项目

```shell
cd project_path_main
git init
git remote add origin project_repo_url
git add .
git commit -m "init"
git push -u origin main
```

### 初始化要嵌入的项目
```shell
cd submodule_path
git init
git remote add origin submodule_repo_url
git add .
git commit -m "init"
git push -u origin main
```

### 初始化子模块
```shell
cd project_path_main
git submodule add submodule-repo-url submodule_path;
```

### 管理
1. 如果你只是修改了子模块里面的内容，主项目里面的内容没有改变，只需要把子模块当成正常的git仓库进行管理即可
2. 如果你只是修改了主项目里面的内容，子模块并没有更改，也是只需要按照正常的git仓库管理流程来管理主项目即可
3. 如果你子模块和主项目都进行了更改需要安装正确的管理方式进行提交
    1. 首先提交子模块中的修改
    ```shell
    cd submodule
    git add .
    git commit -m "xxx"
    git push origin branch
    ```
    2. 回到主项目，更新子模块的引用
    ```shell
    cd path/to/main_project
    # 确认子模块的状态变化
    git status
    git add submodule
    git commit -m "Updated submodule to latest commit"
    ```
    3. 提交主项目的修改
    ```
    git add .
    git commit -m  "xxx"
    git push origin branch
    ```

### 注意
如果你是刚克隆下来这个项目，你的子模块并不会自动初始化你需要执行
```shell
git submodule init
git submodule update
```
来初始化你的子模块

## git如何拉取远程仓库最新状态
在 Git 中，可以通过以下命令来拉取远程仓库的最新状态：

```bash
git fetch origin
```

或者更常用的方式：

```bash
git pull origin <分支名>
```

下面详细说明这两个命令的作用和区别：

1. **`git fetch origin`**：这个命令会从远程仓库拉取最新的更新，但不会自动合并到当前分支。它只是更新了本地的远程分支引用（例如 `origin/main`），如果想要将这些更改合并到当前分支，需要手动使用 `git merge`。这种方式适合想先查看远程改动，然后决定是否合并的情况。

   示例：
   ```bash
   git fetch origin
   git log origin/main --oneline  # 查看远程的更新
   git merge origin/main          # 手动合并到当前分支
   ```

2. **`git pull origin <分支名>`**：`git pull` 等同于 `git fetch` + `git merge`。它会直接将远程仓库中指定分支（如 `main`）的最新更改拉取并合并到当前分支。注意，`git pull` 如果遇到冲突时需要手动解决。

   示例：
   ```bash
   git pull origin main  # 拉取并合并远程 main 分支的最新更改
   ```

### 注意事项
- 在使用 `git pull` 或 `git fetch` 之前，建议先 `git status` 确保当前工作区是干净的（没有未提交的更改）。
- 若在拉取过程中遇到冲突（conflict），可以手动解决冲突后提交（`git add` 和 `git commit`）。

## 拉取远程仓库如果存在子模块应该怎么拉取
在 Git 中，如果项目中有子模块（submodule），那么在拉取远程仓库最新状态时需要额外的步骤，因为子模块不会自动更新，需要手动操作。
以下是拉取包含子模块的仓库最新状态的步骤：

1. **拉取主仓库的最新状态**：

   先拉取主仓库的更新（不包含子模块）：
   ```bash
   git pull origin <分支名>
   ```

2. **更新子模块**：

   然后，更新子模块到最新的版本。可以使用以下命令：
   ```bash
   git submodule update --init --recursive
   ```

   这个命令会初始化（如果还没有初始化的话）并递归地更新所有子模块到主仓库所记录的最新提交版本。

3. **拉取子模块的最新变动（如果子模块有远程更新）**：

   如果你希望子模块直接拉取远程仓库的最新更改而不是仅更新到主仓库记录的版本，可以在每个子模块目录下执行 `git pull`，或者在主仓库目录直接使用以下命令拉取所有子模块的最新更新：

   ```bash
   git submodule foreach git pull origin <子模块分支名>
   ```

### 示例流程

假设你有一个主仓库和一个子模块仓库，操作流程如下：

```bash
# 1. 拉取主仓库最新状态
git pull origin main

# 2. 更新子模块到主仓库记录的版本
git submodule update --init --recursive

# 3. 如果子模块有远程仓库更新，拉取子模块的最新状态
git submodule foreach git pull origin main
```

### 如果子模块没有关联到分支
Git 仓库处于“detached HEAD”状态。HEAD detached 的状态意味着你当前并未位于某个分支上，而是直接指向了一个具体的提交（commit）。在这种状态下进行的任何提交（如 git commit），都不会附属于某个分支.
如果希望这些修改保存在某个分支中，你可以执行以下步骤：

- 新建分支并切换：

这会在当前的 detached HEAD 状态上创建一个新的分支，并将新提交的内容保存到该分支。
```
git checkout -b <新分支名>
```

- 将 HEAD 恢复到已有分支： 如果你不希望新建分支，可以切回到已有的某个分支（比如 main 或其他分支名），并将提交 cherry-pick 到该分支上。

``` bash 
# 切换到某个已有分支
git checkout <分支名>

# 使用 cherry-pick 应用刚才的提交
git cherry-pick 308a3a9

```

### 注意事项

- 子模块更新完后，如果子模块内容发生变化，需要提交主仓库的 `.gitmodules` 或子模块更新的版本变动。
- 子模块的管理和更新较为复杂，建议定期检查子模块状态，并确认子模块是否需要同步最新的远程更改。 Git 中，如果项目中有子模块（submodule），那么在拉取远程仓库最新状态时需要额外的步骤，因为子模块不会自动更新，需要手动操作。

