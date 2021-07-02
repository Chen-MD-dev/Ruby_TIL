active record association은 두 모델간의 연결을 의미한다

저자와 책 모델을 예로 들자
```ruby
class Author < ApplicationRecord
end

class Book < ApplicationRecord
end
```

기존 저자의 새 책 추가
```ruby
@book = Book.create(published_at: Time.new, author_id: @author.id)
```
저자 삭제하면서 모든 책도 삭제
```ruby
@book = Book.where(author_id: @author.id)
@book.each do |book|
  book.destroy
end
@author.destroy
```

그러나 Active Record Association 사용하면

두 모델이 연결되었다는것을 rails에 미리 알려주므로

위와 예제보다 훨씬 간소화된 작업을 할 수 있다

```ruby
class Author < ApplicationRecord
  has_many :books, dependent: :destroy
end

class Book < ApplicationRecord
  belongs_to :author
end
```

이렇게 active record association으로 선언하면

`@book = @author.books.create(published_at: Time.now)`

`@author.destroy`

이렇게 훨씬 간소화 된다

---
# association 유형
+ belongs_to
+ has_one
+ has_many
+ has_many :through
+ has_one :through
+ has_and_belongs_to_many

## belongs_to

선언한 모델의 인스턴스가 다른 모델의 인스턴스에 종속하도록 1:1 매칭을 설정
```ruby
class Book < ActiveRecord::Base
  belongs_to :author
end
```
![image](https://user-images.githubusercontent.com/78338584/124225440-6c865700-db42-11eb-8cad-b6f2d82485ae.png)

## has_one

이것도 1:1 관계지만 조금 다른데

하나의 모델이 인스턴스가 다른 모델의 인스턴스를 소유하거나 포함하는것을 의미
```ruby
class Supplier < ActiveRecord::Base
  has_one :account
end
```
![image](https://user-images.githubusercontent.com/78338584/124225928-372e3900-db43-11eb-89ae-0e61d9444e1b.png)


## has_many

1:N 매칭을 의미 belongs_to의 반대 개념

모델의 인스턴스를 하나도 가지고 있지 않거나 복수개를 가지고 있을 때 사용
```ruby
class Customer < ActiveRecord::Base
  has_many :orders
end
```
![image](https://user-images.githubusercontent.com/78338584/124226141-8ecca480-db43-11eb-8196-70f3b3408112.png)

## has_many :through

N:N 매칭을 의미 다른 복수개의 모델을 제 3의 모델을 통해 연결
```ruby
class Physician < ActiveRecord::Base
  has_many :appointments
  has_many :parients, :through => :appointments
end

class Appointment < ActiveRecord::Base
  belongs_to :physician
  belongs_to :patient
end

class Patient < ActiveRecord::Base
  has_many :appointments
  has_many :physicians, :throught => :appointments
end
```
![image](https://user-images.githubusercontent.com/78338584/124226492-2b8f4200-db44-11eb-983c-ceb39b19dbfb.png)

join model은 다음과 같이 활용 가능
`physician.patients = patients`

새로 연결된 객체에 대해 새 join model이 자동으로 생성된다

has_many:through는 중첩된 has_many를 통해 shortcuts를 설정하는데 유용

예를 들어 document에 sections이 많고

section에 paragraphs이 많은 경우

document의 모든 paragraphs을 간단하게 수집할 수 있다
```ruby
class Document < ActiveRecord::Base
  has_many :sections
  has_many :paragraphs, through: :sections
end

class Section < ActiveRecord::Base
  belongs_to :document
  has_many :paragraphs
end

class Paragraph < ActiveRecord::Base
  belongs_to :section
end
```

through:sections를 지정하면 아래와 같이 코드 표현이 가능하다

`@document.paragraphs`

## has_one :through

다른 모델과 1:1 매칭이나 제 3자 모델을 거쳐서 인스턴스 일치하는것이 다른점

예를들면 각 supplier에 하나의 account가 있고

각 account가 하나의 account history와 연결된 경우 suplier model은 다음과 같다
```ruby
class Supplier < ActiveRecord::Base
  has_one :account
  has_one :account_history, through: :accont
end

class Account < ActiveRecord::Base
  belongs_to :supplier
  has_one :account_history
end

class AccountHistory < ActiveRecord::Base
  belongs_to :account
end
```
![image](https://user-images.githubusercontent.com/78338584/124228297-fdf7c800-db46-11eb-8681-08577b3a553f.png)

## has_and_belongs_to_many

다른 모델 개입 없이 직접 N:N 매칭을 의미

예를 들면 assembly와 part가 있을때

각 assembly에 많은 part가 있고

각 part는 많은 assembly에 나타나는 경우
```ruby
class Assembly < ActiveRecord::Base
  has_and_belongs_to_many :parts
end

class Part < ActiveRecord::Base
  has_and_belongs_to_many :assemblies
end
```
![image](https://user-images.githubusercontent.com/78338584/124228802-ac9c0880-db47-11eb-9eb2-837f9bfaacac.png)

이외에도 여러 association이 존재하는데 오늘은 여기까지 그럼 안녕
