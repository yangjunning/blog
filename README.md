# 杨俊宁的博客

怕什么真理无穷，进一寸有一寸的欢喜

- [Hexo遇上Travis-CI：可能是最通俗易懂的自动发布博客图文教程](https://juejin.im/post/5a1fa30c6fb9a045263b5d2a)

## 命令行指令

### Git 全局设置

```
git config --global user.name "yangjunning"
git config --global user.email "youngjuning@aliyun.com"
```

### 创建新版本库

```
git clone https://github.com/youngjuning/blog.git
cd test
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master
```

### 已存在的文件夹

```
cd existing_folder
git init
git remote add origin https://github.com/youngjuning/blog.git
git add .
git commit -m "Initial commit"
git push -u origin master
```

### 已存在的 Git 版本库

```
cd existing_repo
git remote rename origin old-origin
git remote add origin http://192.168.2.112/yangjunning/test.git
git push -u origin --all
git push -u origin --tags
```
