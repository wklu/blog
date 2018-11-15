# Git常见错误

## 

> ## 1.命令：git pull

> ###  1.1.error-merge

> ```text
> error: Your local changes to the following files would be overwritten by merge：xxxxxxxxx
> Please commit your changes or stash them before you merge.
> ```

 方法一：stash

```text
git stash
git pull
git stash pop
```

 方法二：直接完全覆盖本地修改

```text
git reset --hard
git pull
```

