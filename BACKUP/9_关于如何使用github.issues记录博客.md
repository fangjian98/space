# [关于如何使用github issues记录博客](https://github.com/fangjian98/space/issues/9)

## 关于如何使用github issues记录博客

**来源**

[https://github.com/yihong0618/gitblog](https://github.com/yihong0618/gitblog)

**功能**
1. 纯粹的写issue
2. 写完issue后添加目录到README
3. 使用GitHub Action自动分类：只需要写issue打label就好了，其余完全不用管，核心代码在main.py
4. 支持了 RSS
5. 支持备份BACKUP：Issues并不能随Clone仓库存档保留，此功能用于迁移保存你的Issues

**实现**

1. Fork这个仓库或者拷贝几个必要文件( `.gthub/workflow/generate_readme.yml` `main.py` `requirements.txt` `.gitignore`)。
2. 生成Token：[Personal access tokens]((https://github.com/settings/tokens))，点击New personal access token，填写Note、Expiration，把需要的`Select scopes`的都选上即可，用于使用[Github API](https://docs.github.com/)，完成后生成token，复制该token。
3. 设置 Actions secrets：Settings - Secret - Actions secrets - new repository secret，填写Name和Value，Name自定义，Value为上一步生成是Token，Add secret即可。
4. 修改`.gthub/workflow/generate_readme.yml`：需要把 env 中的 name 和 email 改成自己的，token name 换成你自己的即可，有可能还需要修改分支，默认是`master`，现在默认创建是`main`。
5. 现在就可以添加Issues和Labels，来愉快的记录博客了！

**注意点**

1. Github Actions报错： remote Permission to xx/xx.git denied to github-actions[bot]

解决方案：在仓库设置修改Workflow Permissions为Read and write permissions即可。

![image](https://user-images.githubusercontent.com/59403187/166147147-7f6e28b2-e8ee-47d3-8fb3-d7ed38ddcee4.png)

2. Github Actions运行成功，但未更新BACKUP和README.md

查看log：发现未能提交新增文件

```bash
On branch main
Your branch is up to date with 'origin/main'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	BACKUP/1_remote.Permission.to.git.denied.to.github-actions[bot].md
	BACKUP/5_This.is.my.first.issue.blog.md
	BACKUP/6_This.is.my.second.blog.md
	README.md
	feed.xml

nothing added to commit but untracked files present (use "git add" to track)
nothing to commit
Everything up-to-date
```
解决方案：修改 `.gthub/workflow/generate_readme.yml`

```bash
#  将
git commit -a -m 'update new blog' || echo "nothing to commit"
# 修改为
git add -A
git commit -m 'update new blog' || echo "nothing to commit"
```
3. 原本`.gthub/workflow/generate_readme.yml`的小错误

```bash
# 将
git config --local user.name "${{ env.GITHUB_EMAIL }}"
# 修改为
git config --local user.name "${{ env.GITHUB_NAME }}"
```

4. 创建新的Issue时，需要填写标题和内容，内容为空会导致Github Actions失败，主要原因是main.py报错。

5. BACKUP里的文件并不是唯一，和Issue id并不是绑定的，当你修改某个存在的Issue的标题时，会再次生成一个`id_*.md`，不会删除或覆盖之前的文件，主要原因是main.py未优化此问题。