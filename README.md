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

#页面美化体系框架
gem 'bootstrap-sass', '~> 3.3', '>= 3.3.7'
bundle install
```
app/assets/stylesheets/application.scss
@import "bootstrap-sprockets";
@import "bootstrap";
```
app/views/links/-form.html.erb
```
<%= form_for(@link) do |f| %>
  <% if @link.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@link.errors.count, "error") %> prohibited this link from being saved:</h2>

      <ul>
      <% @link.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
      </ul>
    </div>
  <% end %>

  <div class="form-group">
    <%= f.label :title %><br>
    <%= f.text_field :title, class: "form-control" %>
  </div>
  <div class="form-group">
    <%= f.label :url %><br>
    <%= f.text_field :url, class: "form-control" %>
  </div>
  <br>
  <div class="form-group">
    <%= f.submit "Submit", class: "btn btn-lg btn-primary" %>
  </div>

<% end %>
```
app/views/links/edit.html.erb
```
<h1>Editing link</h1>

<%= render 'form' %>

<%= link_to 'Show', @link %> |
<%= link_to 'Back', links_path %>

```
app/views/links/index.html.erb
```
<% @links.each do |link| %>
  <div class="link row clearfix">
    <h2>
      <%= link_to link.title, link %><br>
      <small class="author">Submitted <%= time_ago_in_words(link.created_at) %> by <%#= link.user.name %></small>
    </h2>

    <div class="btn-group">
      <a class="btn btn-default btn-sm" href="<%= link.url %>">Visit Link</a>
      <%#= link_to like_link_path(link), method: :put, class: "btn btn-default btn-sm" do %>
        <span class="glyphicon glyphicon-chevron-up"></span>
        Upvote
        <%#= link.get_upvotes.size %>
      <%# end %>
      <%#= link_to dislike_link_path(link), method: :put, class: "btn btn-default btn-sm" do %>
        <span class="glyphicon glyphicon-chevron-down">
        Downvote
        <%#= link.get_downvotes.size %>
      <%# end %>
    </div>
  </div>
<% end %>

```
app/views/links/new.html.erb
```
<h1>New Link</h1>

<%= render 'form' %>

<%= link_to 'Back', links_path %>

```
app/views/links/show.html.erb
```
<div class="page-header">
  <h1><a href="<%#= @link.url %>"><%= @link.title %></a><br> <small>Submitted by <%#= @link.user.name %></small></h1>
</div>

<div class="btn-group">
	<%= link_to 'Visit URL', @link.url, class: "btn btn-primary" %>
</div>

<div class="btn-group pull-right">
  <%#= link_to like_link_path(@link), method: :put, class: "btn btn-default btn-sm" do %>
    <span class="glyphicon glyphicon-chevron-up"></span>
    Upvote
    <%#= @link.get_upvotes.size %>
  <%# end %>
  <%#= link_to dislike_link_path(@link), method: :put, class: "btn btn-default btn-sm" do %>
    <span class="glyphicon glyphicon-chevron-down">
    Downvote
    <%#= @link.get_downvotes.size %>
  <%# end %>
</div>

<% if @link.user == current_user -%>
	<div class="btn-group">
		<%= link_to 'Edit', edit_link_path(@link), class: "btn btn-default" %>
		<%= link_to 'Destroy', @link, method: :delete, data: { confirm: 'Are you sure?' }, class: "btn btn-default" %>
	</div>
<% end %>

<h3 class="comments_title">
  <%#= @link.comments.count %> Comments
</h3>

<div id="comments">
  <%#= render :partial => @link.comments %>
</div>
<%#= simple_form_for [@link, Comment.new]  do |f| %>
  <div class="field">
    <%#= f.text_area :body, class: "form-control" %>
  </div>
  <br>
  <%#= f.submit "Add Comment", class: "btn btn-primary" %>
<%# end %>

```
