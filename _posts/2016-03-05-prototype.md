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
- 생성자 함수에 의해 인스턴스화된 객체들은 prototype을 참조하는 포인터가 생성되며 **[[Prototype]]**이라고 부른다. FF, Safari, Chrome에서는 **__proto__ 프로퍼티**를 지원하며 프로퍼티 접근이 가능하다.
- 즉 [[Prototype]]은 prototype을 참조하는 것이지, 생성자 함수를 직접적으로 참조하는 것이 아니다.

~~~~javascript
function Person() {} // 생성자 함수

Person.prototype.getName = function() {
  return this.name;
};

var man = new Person(), // 인스턴스 객체 1
    woman = new Person(); // 인스턴스 객체 2

man.name = 'tom';
woman.name = 'jerry';

console.log(man.getName());
console.log(wonam.getName());
~~~~
![ScreenShot](/screenshot/prototype_sample_02.png)

### 프로토타입 링크 



