# Git常见错误

## 1.git pull

#### Q:如下

> ```text
> error: Your local changes to the following files would be overwritten by merge：xxxxxxxxx
> Please commit your changes or stash them before you merge.
> ```

####  A1：stash

```text
git stash
git pull
git stash pop
```

####  A2：直接完全覆盖本地修改

```text
git reset --hard
git pull
```

