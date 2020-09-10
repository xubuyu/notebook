# Git

### 创建 git 版本库

```bash
mkdir hello-git
cd hello-git
git init
```

### 工作区、缓存区与本地库

```bash
git add .
```

```bash
git commit -m "更新说明"
```

```bash
git log
```

### 版本回退

```bash
git reset --hard <id>
```

### 查看分支

```bash
git branch
git branch -v
```

### 创建分支

```bash
git branch dev
```

```bash
git checkout -b dev
```

### 切换分支

```bash
git checkout dev
```

### 合并分支

```bash
git checkout master
git merge dev
```

### 远程仓库

```bash
git push
```

```bash
git clone
```

```bash
git pull
```



