기존의 언어에서 좀 달랐던거만 적어본다

* 변수 앞에 $는 전역변수다
* 변수 앞에 @는 인스턴스 변수다
* 변수 앞에 @@는 클래스 변수다
* unless는 if not 개념이다
* 예를들어 0..2는 0,1,2 로 range의 개념인듯 + 마지막숫자도 포함한다
* 0...2는 0,1 로 마지막 숫자는 포함하지 않는다

---
block
변수에 담고 싶지 않을때 무명함수로 사용하는 감싸져 있는 코드블록이다
* do ~ end : multi line을 block으로 감싸고 싶을 때
* { } : inline으로 감쌀 때

함수의 인자에는 단 하나의 block만 넘겨줄 수 있다
넘겨준 블록을 실행할 때 반드시 proc로 변환해줘야 한다 ( &block, yield )

#블록이 자주 쓰이는 패턴
each, map, select등 반복 실행을 위한 메소드에서 블록을 자주 사용함
each example
```ruby
array = [1, 2, 3, 4]

array.each do |num|
  p num
end
```

map example
```ruby
array = [1, 2, 3, 4]

ret = array.map do |num|
  num + 10
end
p ret
```

select example
```ruby
array = [1, 2, 3, 4]

ret = array.select do |num|
  num if num % 2 == 0
end
p ret
```
---
#블록을 변수에 넣어 유지하는 법
블록 자체를 변수에 보관할 수는 없다
하지만 Proc오브젝트에 블록을 넘겨줌으로서 proc오브젝트로써 변수에 저장이 가능
```ruby
proc = Proc.new {|w| puts w}
```
블록 내에서 선언한 변수는 전부 로컬변수로 블록 밖 (do~end. {}) 에서 참조할 수 없다.
블록 내에 있는 변수를 *블록스코프의 로컬변수* 라고 부른다
```ruby
array = [1, 2, 3, 4]
local = 'local'

array.each do |num|
  block = 'block'
end

p local   # =>
p block   # =>
```
result
```ruby
"local"
Traceback (most recent call last):
ex2.rb:9:in `<main>': undefined local variable or method `block' for main:Object (NameError)
```
결과에서 알 수 있듯 블록내에서 선언한 block변수는 밖에서 참조할 수 없다.
##block_given
block변수를 넘긴 해당 메소드에서 블록이 넘어왔는지 `block_given?`으로 체크가 가능
```ruby
def block_given_test
  puts block_given?
end

block_given_test  #<<--block을 변수로 넘겨주지 않았으므로 false
block_given_test{
  puts "papa"
}
block_given_test do
  puts "mama"
end
```
result
```ruby
false
true
true
```
---
# yield사용시 주의사항
yield가 있는 메소드 호출시 반드시 인자에 block을 넘겨줘야 한다
Proc 넘겨주든 문자열을 넘겨주든 에러 #ArgumentError#가 발생한다
당연히 아무것도 안 넘겨주면 #LocalJumpError#가 발생
```ruby
def test
  yield
end
#test # no block given (yield) (LocalJumpError)

#test Proc.new { pp 'test'} # wrong number of arguments (given 1, expected 0) (ArgumentError)

#test 'test' # wrong number of arguments (given 1, expected 0) (ArgumentError)

test { pp 'test'}
```
