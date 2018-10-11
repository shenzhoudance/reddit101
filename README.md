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

# 两种推送云端的方法
git checkout master
git merge link_scaffold
git push origin master
```

# 添加一个 add_user 的分支
```
git checkout -b add_user
gem 'devise', '~> 4.5'
bundle install
rails g devise:install
rails g devise views
rails g devise User
rake db:migrate
```
# 注册一个用户的数据操作
```
rails c
@user = User.find
exit

```
# 本地保存修改的代码
```
git status
git add .
git commit -m "add devise and create user mode"
```

#功能和用户的对应关系
app/models/user.rb
belongs_to :links
app/models/link.rb
has_many :user
rails g migration add_user_id_to_links user_id:integer:index
rake db:migrate
```
2.3.1 :010 >rails c
2.3.1 :010 >Link
2.3.1 :010 >Link.connection
2.3.1 :010 >Link
```
```
 => Link(id: integer, title: string, url: string, created_at: datetime, updated_at: datetime, user_id: integer)
```
2.3.1 :010 >exit

git status
git add .
git commit -m "Add Association Between Link and User"

app/controllers/links_controller.rb
```
Link.new
改为：current_user.links.build（创建新建必须是用户）
添加：before_action :authenticate_user!, except: [:index, :show]（不是用户就需要注册用户）
```

```
2.3.1 :010 >rails c
2.3.1 :010 >Link
2.3.1 :010 >Link.connection
2.3.1 :010 >@link = Link.last
```
Link Load (0.2ms)  SELECT  "links".* FROM "links" ORDER BY "links"."id" DESC LIMIT ?  [["LIMIT", 1]]
=> #<Link id: 3, title: "才华横溢", url: "", created_at: "2018-10-11 08:47:07", updated_at: "2018-10-11 08:47:07", user_id: 1>
```
2.3.1 :010 >exit
```
# 数据用户的保存
```
rails c
@link = Link.last
@user = User.first
@link = Link.find(2)
@link.user = User.first
@link.save
```
