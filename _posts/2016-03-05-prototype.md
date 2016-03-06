## 프로토타입(Prototype)

* 자바스크립트의 객체 개념까지 이해하고 있다는 전제하에 설명을 진행한다.

### 프로토타입의 이해
- 함수를 정의하면 메모리 상에 **프로토타입 객체**가 생성된다. 정의된 함수를 확인하면 prototype 프로퍼티가 있는데 이 프로퍼티는 프로토타입 객체에 해당된다.
- 프로토타입 객체는 **자신이 다른 객체의 원형**이 되는 객체이다.
- new 연산자를 통해 정의된 함수로 객체를 생성(인스턴스화)하면, 생성된 객체는 프로토타입 객체를 가리키는 **숨은 링크(포인터)**를 가지게 된다.
- 포인터를 통해 인스턴스 객체들이 공통의 멤버(속성, 메서드)에 접근할 수 있다. **공통의 멤버**는 결국 포인터가 참조하는 프로토타입의 멤버에 해당된다.
- 프로토타입의 특성을 이용하면 자바스크립트에서 **클래스를 흉내**내고 **상속**도 구현할 수 있다.  

### prototype Vs. [[Prototype]]
- 실제 코드 상에서 사용되는 프로토타입은 2가지로 구분된다.
- (대다수가 잘못 알고 있는) **prototype 프로퍼티**는 커스텀 객체에서 사용되는 일반 프로퍼티와는 다르다. 생성자 함수가 선언될 때 constructor 프로퍼티로 생성자 함수를 참조하면서 새로 생성되는 객체이다.
- 생성자 함수에 의해 인스턴스화된 객체들은 prototype을 참조하는 포인터가 생성되며 **[[Prototype]]**이라고 부른다. FF, Safari, Chrome에서는 **\_\_proto\_\_ 프로퍼티**를 지원하며 브라우저 상에서 직접 프로퍼티의 접근이 가능하다.
- 즉 [[Prototype]]은 prototype을 참조하는 것이지, 생성자 함수를 직접적으로 참조하는 것이 아니다.
![ScreenShot](/screenshot/prototype/prototype_preview.jpg)

### 프로토타입 동작 원리
- 프로토타입(prototype) 객체 생성부터 인스턴스 생성 시 프로토타입 참조 등 내부적으로 연결된 링크들을 통해 동작을 설명한다.

Step 1. 생성자 함수 정의
~~~~javascript
function Person() {}
~~~~  
![ScreenShot](/screenshot/prototype/prototype_logic_01.jpg)  

Step 2. 프로토타입 객체에 멤버(속성, 메서드) 선언
~~~~javascript
Person.prototype.name = 'tom'; // 속성
Person.prototype.getName = function() { // 메서드
  return this.name;
};
~~~~  
![ScreenShot](/screenshot/prototype/prototype_logic_02.jpg)  

Step 3. 인스턴스 생성 
~~~~javascript
var developer = new Person(),
    designer = new Person();
~~~~  
![ScreenShot](/screenshot/prototype/prototype_logic_03.jpg)  

Step 4. 생성된 인스턴스와 인스턴스 객체가 프로토타입 객체를 정상적으로 참조하고 있는지 확인  
~~~~javascript
console.log(developer instanceof Person); // true 반환
console.log(designer instanceof Person); // true 반환

console.log(developer.hasOwnProperty('name')); // false 반환
console.log(developer.hasOwnProperty('getName')); // false 반환
console.log(Person.prototype.hasOwnProperty('name')); // true 반환
console.log(Person.prototype.hasOwnProperty('getName')); // true 반환
~~~~  
![ScreenShot](/screenshot/prototype/prototype_logic_04.jpg)  

Step 5. 생성된 인스턴스의 속성 값을 출력하면서 객체간 관계를 확인
~~~~javascript
developer.name = 'jerry';

console.log(developer.hasOwnProperty('name')); // true 반환
console.log(designer.hasOwnProperty('name')); // false 반환

console.log(developer.getName()); // 'jerry' 반환
console.log(designer.getName()); // 'tom' 반환
~~~~  
![ScreenShot](/screenshot/prototype/prototype_logic_05.jpg)


### 프로토타입을 이용해 커스텀 객체 생성하기 (클래스 흉내내기)
- **생성자 패턴**과 **프로토타입 패턴**을 조합한다.
  - 생성자 패턴 : 생성자 함수 내에 프로퍼티와 메서드를 선언한다. 인스턴스가 생성될 떄마다 선언된 메서드가 생성되어 메모리를 많이 차지하게 되는 단점이 있다.
  ~~~~javascript
  function Building(floor) {
    this.floor = floor;
    this.getFloor = function() { ... }
  }
  
  var b = new Building(10);
  ~~~~
  - 프로토타입 패턴 : 프로토타입 객체에 프로퍼티와 메서드를 선언한다.
  ~~~~javascript
  function Building() {}
  
  Building.prototype.getFloor = function() { ... }
  
  var b = new Building();
  ~~~~
  - **생성자 패턴**으로 **인스턴스의 프로퍼티**를 정의, **프로토타입 패턴**으로 **메서드와 공유 프로퍼티**를 정의한다. 이는 메모리를 절약하는 효과가 있다.
  ~~~~javascript
  function Building(floor) {
    this.floor = floor;
  }
  
  Building.prototype.getFloor = function() {
    return this.floor;
  }
  
  var b = new Building();
  ~~~~

### 프로토타입과 상속
- 기본 상속
  - 자바스크립트에서 **상속**은 코드 재사용 이슈에 해당된다.
  - **프로토타입 체이닝(Prototype Chaining)** : 자식 생성자 함수의 인스턴스가 참조하는 프로토타입 객체를 부모 생성자 함수의 인스턴스로 할당한다.
  - 코드에서 **Child.prototype = new Parent();** 에 해당 (inherit 함수를 정의하여 사용할 수도 있다)
  - 이미지에서 **빨간색 링크**에 해당
  ~~~~javascript
    this.firstName = firstName;
    this.lastName = lastName;
  }

  function Child(lastName) {
    this.lastName = lastName;
  }

  Parent.prototype.getFullName = function() {
    return this.firstName + ' ' + this.lastName;
  };

  Child.prototype = new Parent('Ryu', 'Sun'); // 상속

  Child.prototype.getName = function() {
    return this.lastName;
  };

  var developer = new Child('Sunny');

  console.log(developer.getName()); // 'Sunny' 출력
  console.log(developer.getFullName()); // 'Ryu Sunny' 출력
  ~~~~
  ![ScreenShot](/screenshot/prototype/prototype_chain.jpg)
- 상속의 기법
  - 생성자 훔치기
  ~~~~javascript
  ~~~~
  - 조합 상속
  - 생성자 훔치기
  ~~~~javascript
  ~~~~
  - **프로토타입 상속**
  - 생성자 훔치기
  ~~~~javascript
  ~~~~
  - 기생 상속
  - 생성자 훔치기
  ~~~~javascript
  ~~~~
  - 기생 조합 상속
    - 생성자 훔치기
  ~~~~javascript
  ~~~~



