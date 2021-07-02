컨트롤러 안에 있는 메소드를 액션이라 한다

routes.rb에서 `root 'home#index'` 의 의미는

주소로 처음 들어가면 HomeController에서 index엑션으로 연결한다 이다

`get '/' => 'home#index'` 로도 동일하게 가능하다

`{메소드타입} '{path}' => {컨트롤러이름}#{액션이름}` 이런 의미이다

view단에서 루비 문법을 사용할때 기본은 `<%  %>` 이다

view에서 변수를 받아서 출력하는 부분은 `<%= %>` 이렇게 표현될 수 있다

보다시피 `<%`는 실행한다는 의미 `<%=`는 출력까지 한다는 의미이다

```ruby
<% if true %>
<p>hello, world!</p>
<% end %>

<% if false %>
<p> not print </p>
<% end %>
```
변수 전달 기본적인 방법은

controller -> view : `<%= @{인스턴스변수명} %>` 컨트롤러가 클레스라서 인스턴스 변수를 쓴다(?) 아무튼 그럼

view -> controller : `params[:{태그명}]`

---

## 정보입력방법
+ Form테그

form테그는 form테그와 input테그로 구성되어 있다

form테그는 정보를 묶어서 보내는 테그
> method속성 : 어떤 방식으로 정보 전달 (post, get, etc...)
> 
> action속성 : 어디로 정보 전달 (path)

input테그는 정보를 받는 테그
> type속성 : 어떤 정보를 받을지 (box)
> 
> name속성 : 정보의 이름 (id)

+ url
routes.rb에서

`get 'test/:x/:y' => 'home#test'`

이런식으로 url로도 역으로 변수전달이 가능하다

---
만약 post 사용시 에러 발생하면

application_controller.rb 에서

`protect_from_forgery with: :exception` 를 주석처리한다(rails 4버전 기준)
