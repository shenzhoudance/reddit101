# How to build a Reddit or Hacker News Style Web App in Rails
```
rails new reddit
cd reddit
git init
git status
git add .
git commit -m "Commit Initial"
```
```
git checkout -b link_scaffold
rails g scaffold links title:string url:string
rake db:migrate

root 'links#index'
```
```
git status
git add .
git commit -m "add link_scaffold"
```
