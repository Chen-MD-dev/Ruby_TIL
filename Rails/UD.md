이 글에서 쓰일 새 개념

redirect_to "url" : 해당 url로 가라

redirent_to :back : 이전 페이지로 가라

`Post.find(pararms[:post_id])`

post_id로 인덱싱하면 해당 id의 모든 값을 가져올 수 있다

/update => 글 수정 action

/modify => 글 수정 form

/delete => 글 삭제 action

이런 구성의 예
routes.rb
```ruby
get "/modify/:post_id" => "home#modify"
post "/update/:post_id" => "home#update"
get "/delete/:post_id" => "home#delete"
```
home_controller.rb
```ruby
def modify
  @post = Post.find(params[:post_id])
end

def update
  post = Post.find(params[:post_id])
  post.title = params[:title]
  post.content = params[:content]
  post.save
  redirect_to '/index'
end

def delete
  Post.destroy(params[:post_id])
  redirect_to :back
end
```
modify.erb
```ruby
<form action="/update/<%=@post.id%>" method="post">
  title: <input type="text" name="title" value="<%=@post.title%>"/>
  content: <input type="text" name="content" value="<%=@post.content%>"/>
  <input type="submit"/>
</form>
<a href="/index">back</a>
```
