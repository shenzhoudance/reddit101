# How to build a Reddit or Hacker News Style Web App in Rails

# 本地新建一个专案，然后项目初始化
```
rails new reddit
cd reddit
git init
git status
git add .
git commit -m "Commit Initial"
```
# 新建一个 link_scaffold 的分支
```
git checkout -b link_scaffold
rails g scaffold links title:string url:string
rake db:migrate
```
# 修改路由器的设置
```
atom .
config/routes.rb
root 'links#index'
```
# 上传修改的代码在本地和云端
```
git status
git add .
git commit -m "add link_scaffold"
git push origin link_scaffold

两种推送云端的方法
git checkout master
git merge link_scaffold
git push origin master
```
