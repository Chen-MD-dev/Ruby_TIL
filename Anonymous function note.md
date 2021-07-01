루비의 특징적인 문법중 하나는 블록이다
루비에서는 블록문법을 통해서 하나의 익명함수를 손쉽게 메서드에 넘겨줄 수 있다
루비에서는 블록이 자주 사용되기에 익명함수의 개념을 이해해야 한다
블록 말고도 익명 함수 객체를 생성할 수 있는데 바로 #Proc객체#와 #lambda문#을 사용하는것이다

---
# Proc 객체 이해하기
루비에서는 Proc 클래스를 통해서 익명 함수를 생성할 수 있음
여기서 Proc은 Procedure줄임말로 어떤 처리 과정(절차)를 담고 있다
Proc또한 일반적인 루비 클래스와 다르지 않으므로 Proc.new메서드로 객체를 생성할 수 있다
```ruby
Proc.new
# ArgumentError: tried to create Proc object without a block
```
Proc.new 메서드는 블록으로 절차(루비 표현식들)를 넘겨받는다
루비에서는 메서드 뒤에 do ~ end 형태로 블록이라는 특별한 문법을 사용할 수 있다
그리고 이 do ~ end 사이에 #루비 표현식# 이 들어간다
블록 안의 표현식들은 바로 실행되지 않고 익명 함수로서 메서드에 전달 됩니다.
중요한 것은 루비 표현식들이 고스란히 함수로 전달된다는 점이다!
```ruby
Proc.new do
  puts "hello, world!"
end
# => #<Proc:0x007f99f12c6bf8@(pry):2>
```
Proc.new는 Proc 객체를 반환합니다. 이 생성자 메서드는 넘겨받은 익명함수에 대해서
어떤 일도 하지 않고 그대로 저장해 둡니다. 그리고 앞서 예기했든 블록에 쓰여진 루비표현식은
곧바로 실행되지 않습니다. 따라서 `puts "hello, world!"`가 출력되지 않습니다.

# Proc 객체 실행하기
이 Proc 객체는 원하는 시점에 실행할 수 있다
+ `.call() / .() / []` 메서드를 사용한 호출법
```ruby
p = Proc.new { puts "hello, world!" }

p.call()  # hello, world!

p.()  # hello, world!

p[]  # hello, world!
```
형태는 다르지만 결과는 모두 같은걸 볼 수 있다
루비에서는 다른 언어에서 사용하듯 `function()`방식으로 호출할 수 없다
루비에서는 익명 함수(Proc 객체)를 p 변수에 대입했습니다만, 함수처럼 직접 호출하는 것은 불가능하다
```ruby
p()
# NoMethodError: undefined method `a' for main:Object
```
## 함수명으로 호출 
파이썬이나 자바스크립트 같이 함수 이름으로 접근하면 함수 자체에 접근할 수 있고 이를 직접 호출할 수 있지만 루비는 아님
NomethodError 예외가 발생하는 이유는 말그대로 p라는 이름으로 정의된 함수가 존재하지 않기 때문이다.
루비에서는 익명 함수와 기명 함수가 존재하는 공간이 다르기 때문이다 ( 다른 언어는 공간이 같은 경우가 있다 )
---
블록은 엄밀히 말하면 proc 객체는 아니다
단! 메서드 선언시 `&` 연산자를 통해 블록을 명시적으로 proc 객체로 받아올 수 있다
```ruby
def hello(&b)
  b.call()
end

hello do
  puts "hello, world"
end
# hello, world

p = proc { puts "hello, world" }
p.call()
# hello, world
```
---
# Lambda문
루비에는 lambda라는 proc 객체를 생성하는 또 다른 방법이 있다
```ruby
l = lambda{ puts "hello, world" }

l.class   # Proc

l.call()  # hello, world

# ruby 1.9부터는 lambda 대신 신텍스 슈가인 -> 를 사용할 수도 있다
->{ puts "hello, world" }
```
루비에서는 lambda 문으로 생성된 객체가 일반적인 proc객체보다 좀 더 *함수* 답게 작동한다

#Proc#lambda? 를 사용한 lambda 여부 확인
먼저 본격적으로 차이점을 알아보기 전에 Proc 객체와 lambda로 만들어진 개체를 구분하는 방법을 살펴보자
```ruby
Proc.new{}.lambda?  # => false
proc{}.lambda?      # => false
lambda{}.lambda?    # => true
->{}.lambda?        # => true
```
참고로 일반적인 메서드를 객체화해서 Proc 객체로 변환하면 lambda Proc 객체가 된다
```ruby
def hello; end
hello_method = method(:hello)
hello_method.to_proc.lambda?  # => true
```
[여러가지 람다형태](https://ruby-doc.org/core-1.9.3/Proc.html#method-i-lambda-3F)

## 인자 검사 방식의 차이
첫번째 차이점은 lambda로 만들어진 proc 객체는 인자 개수를 엄격하게 검사한다
일반적으로 블록에서는 블록 인자라는 독특한 방법으로 인자를 받는다.
여기서 하나의 인자를 받는 Proc객체를 만들고, 인자 개수를 바꿔가며 실행하는 예이다
```ruby
hello = Proc.new { |name| puts "hello, #{name}!"}
hello.call()
#hello, !

hello.call("jack")
#hello, jack!

hello.call(1, 2, 3, 4)
#hello, 1!
```
블록에서는 하나의 인자로 정의되어 있지만, 인자 개수가 달라지더라도 에러가 발생하지 않습니다.
이런 점에서 Proc 객체는 이름 그대로 ___절차___ 만 저장된 객체라고 할 수 있다.

반면 lambda로 만든 Proc 객체는 다르게 작동한다
```ruby
hello = lambda{|name| puts "hello, #{name}!" }

#신텍스 슈가를 사용할 때는 다음과 같이 정의
->(name){ puts "hello, #{name}!" }

hello.call()
# ArgumentError: wrong number of arguments (0 for 1)

hello.call("Jack")
# hello, Jack!

hello.call(1, 2, 3, 4)
#ArgumentError: wrong number of arguments (5 for 1)
```
인자를 넘기지 않거나 더 많은 인자를 넘긴 경우 ArgumentError 예외가 발생한다

## return 작동 방식의 차이
proc와 lambda의 또 다른 차이점은 return의 작동 방식이다
먼저 proc의 return은
```ruby
def return_two(&p)
  p.call
  retuen 2
end

return_two(&Proc.new { retuen 1 })
# LocalJumpError: unexpected return
```
밖에서 Proc객체를 넘겨받으면 LocalJumpError 예외를 발생시킨다
return이 (Proc객체? or Proc객체를 실행하는 문맥?) 어떻게 해석되어야 하는지 불문명하기 때문
다음은 밖에서 넘겨받는 대신 안에서 Proc객체를 생성하는 예제
```ruby
def return_two()
  p = Proc.new { return 1 }
  p.call
  return 2
end

return_two
# => 1
```
이번에는 1을 반환한다. 놀랍게도 Proc객체의 return문이 return_two의 return으로 실행된 것을 알 수 있다
이런 의도로 Proc객체를 쓰는 일은 거의 없을거다

이번엔 lambda로 만든 proc객체다
```ruby
def return_two(&p)
  p.call
  return 2
end

return_two(&lambda{ return 1 })
# => 2
```
이번엔 2를 반환했다. 자세히 보기위해 p.call의 반환값을 출력해보자
```ruby
def return_two(&p)
  puts p.call
  return 2
end

return_two(&lambda{ return 1 })
# 1
# => 2
```
p.call의 반환값이 1이되는 것을 알 수 있다.
이를 통해 lambda함수에서 return문을 사용하면 Proc객체, 즉 익명함수 자체의 반환이 되는 것을 알 수 있다
따라서 lambda함수에서는 1을 반환하고 return_two 함수에서는 의도한 대로 넘겨준 lambda 객체와는 무관하게 2를 반환한다

## break 작동 방식의 차이
이번에는 break도 return과 비슷한 차이가 나는걸 알아보자
proc객체에서 break를 사용하면 LocalJumpError를 발생시킨다 -> return문과 같다
```ruby
0.upto(3, &Proc.new{|i| puts i; break if i == 2 })
# 0
# 1
# 2
# LocalJumpError: break from proc-closure
```
반면에 람다를 사용하면 break는 람다객체 안으로 한정된다
따라서 반복문 안에서 아무런 영향을 끼치지 않고 i==2 조건을 만족할 때 람다 안에서 break가 실행될 뿐이다
```ruby
0.upto(3, &lambda{|i| puts i; break if i == 2})
# 0
# 1
# 2
# 3
# => nil
```

## block과 Proc객체의 차이
블록은 proc과 비슷하지만 엄밀히 말하면 proc객체와는 조금 다르다
블록은 메서드와 결한된 문맥에서만 존재하기 때문에 이를 Proc객체로 만들기는 어렵다
블록에서는 break가 정상적으로 작동하는 예
```ruby
0.upto(10) { |i| puts i; break if i == 3 }
# 0
# 1
# 2
# 3
# => nil
```
이번에는 정확히 같은 일을 하는 proc객체를 넘겨줘보자
```ruby
0.upto(10, &Proc.new{ |i| puts i; break if i == 3 })
# 0
# 1
# 2
# 3
# LocalJumpError: break from proc-closure
```
LocalJumpError가 발생한다. 이는 넘겨진 함수가 클로저로 실행되는데
그 안에서 break를 사용하고 있기 때문에 발생하는 예외다
순수한(?) 블록에서는 이문제를 적절하게 해결해주는걸 보여준다

# 결론
그럼 이제 이 이상해 보이는 구문이 정상적인 루비 구문이라는걸 이해했을것이다
```ruby
->(){}[]
# nil
```
루비에서 블록과 익명 함수의 개념에 대한 이해는 아무리 강조해도 지나치지 않다!!
proc나 lambda는 Kernel클래스의 메서드라서 문법처럼 보이기도 하고 함수처럼 보이기도 하고
혼동되기 쉬운 요소다. 나아가 lambda에는 `->`라는 신텍스 슈가도 있고
이런 익명 함수를 실행 시키는 방법으로는 `.call(), .(), []` 와 같이 세가지나 있다
어색한 표현일 수 있으나 루비에서 많이 사용되는 표현으로 확실히 익혀두자
