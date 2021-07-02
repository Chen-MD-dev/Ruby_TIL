모델생성의 경우 자동생성 내역은
* 모델 파일
* 마이그레이션 파일
* 테스트 파일

---
form을 만들고(view) 정보를 controller로 전달하고 다시 다른 view로 보여주는 예

write.erb
```ruby
<form action="/home/create" method="post">
  <label>Title</label>
    <input type="text" name="title">
  <label>Content</label>
    <textarea name="content">
  <button type="submit">작성</button>
</form>
```

home_controller.rb
```ruby
def index
  @posts = Post.all
end

def write

end

def create
  post = Post.new
  post.title = params[:title]
  post.content = params[:content]
  post.save
  
  redirect_to '/index'
end
```

index.erb
```ruby
<a href = "home/new">새 글 작성</a>

<%# @posts내용을 각각 가져와서 post라는 지역변수에 저장 %>
<% @posts.each do |post| %>
  <%= post.title %>
  <%= post.content %>
<% end %>
```
