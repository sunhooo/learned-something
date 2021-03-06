# Chapter 4. 텍스트와 바이트

## 시작

Python3 부터는 텍스트 문자열과 바이트 시퀀스를 엄격히 구분하기 시작한다. Python2에서는 암묵적으로 바이트 시퀀스를 유니코드 텍스트로 변환했지만 이젠 과거 얘기다. 

> 전 세계적으로 유니코드가 채택되면서(웹사이트의 80% 이미 UTF-8 사용하고 있음) 텍스트 문자열이라는 개념을 파일에서 저장된 내용을 나타내는 이진 시퀀스와 분리함 

여기서 나오는 문제들 대부분 아스키 텍스트만 사용하는 프로그래머들에게는 영향을 미치지 않지만, 문자열과 바이트는 구분해야 한다. ('1 문자 != 1 바이트')

> [아스키](https://ko.wikipedia.org/wiki/ASCII) 코드는 아스키는 7비트 인코딩으로, 33개의 출력 불가능한 제어 문자들과 공백을 비롯한 95개의 출력 가능한 문자들로 총128개로 이루어진다. 출력 가능한 문자들은 52개의 영문 알파벳 대소문자와, 10개의 숫자, 32개의 특수 문자, 그리고 하나의 공백 문자로 이루어진다. 제어 문자들은 역사적인 이유로 남아 있으며 대부분은 더 이상 사용되지 않는다. 



## 4.1 문자 문제

문자열의 바이트는 사용하는 인코딩에 따라 달라진다. 인코딩은 코드 포인트를 바이트 시퀀스로 변환하는 알고리즘이다.

코드 포인트: 문자열에 할당된 코드값 (1문자 1코드)

- '1 문자 != 1 바이트'

bytes 시퀀스는 알아보기 어려운 기계 메모리 덤프, 유니코드 str은 사람이 읽을 수 있는 텍스트
bytes 시퀀스를 사람이 읽을 수 있는 텍스트로 decoding(해동)하고, str을 저장하거나 전송하기 위해 bytes로 encoding(암호화)한다.



## 4.2 바이트에 대한 기본 지식

- bytes, bytearray, memoryview 등의 이진 시퀀스 자료형을 설명하고 있다.

Python2에서의 bytes와 Python3에서의 bytes는 다른 것이다.
2에서는 단지 str 형의 별명일 뿐, 3의 bytes와는 작동 방식이 다르다. 3에서 소개된 bytes는 불변형이다.

> 리터럴: 변하지 않는 값, 변수에 넣는 변하지 않는 데이터 (boolean, str, int, )
>
> [자바스크립트의 리터럴(iteral)](https://velog.io/@jesop/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%9D%98-%EB%A6%AC%ED%84%B0%EB%9F%B4iteral)

bytes는 불변형, bytearray는 가변형

> https://dojang.io/mod/page/view.php?id=2462
> bytes와 차이점은 요소를 변경할 수 있느냐의 차이입니다. bytes는 요소를 변경할 수 없고, bytearray는 요소를 변경할 수 있습니다.

**[예제 4-2]**

```python
cafe = bytes('cafetería', encoding='utf-8')
cafe
Out[26]: b'cafeter\xc3\xada'	# 리터럴 표기법으로 출력 
cafe[0]
Out[27]: 99	# int형 반환
cafe[:1]
Out[28]: b'c'	# 길이가 1인 bytes 객체 반환
```

이진 시퀀스가 실제로 정수형의 시퀀스이기는 하지만, 각 바이트 값에 따라 다음과 같이 세 가지 형태로 출력된다.

- 화면에 출력 가능한 아스키 문자(공백에서 물결표(~))까지는 아스키 문자 그대로 출력
- 탭, 개행 문자, 캐리지 리턴, 백슬래시는 이스케이프 시퀀스(\t, \n, \r, `\\`)로 출력
- 그 외의 값은 널 바이트를 나타내는 \x00처럼 16진수 이스케이프 시퀀스로 출력

```python
c = str('cafe')
c[0] == c[:1]
Out[34]: True
c[0]
Out[35]: 'c'
c[:1]
Out[36]: 'c'
```

`c[0] == c[:1]` 이 되는 시퀀스는 str이 유일하다.



이진 시퀀스 bytes, bytearray는 포맷팅하는 format, format_map 메서드를 제외하고 str이 제공하는 메서드를 모두 지원한다. 유니코드 데이터에 관련된 메서드를 지원한다. 정규표현식을 컴파일하면 re모듈에서 제공하는 정규 표현식 함수를 적용할 수 있다. 



### 4.2.1 구조체와 메모리 뷰

이진 데이터를 사용한다면 struct, memoryview에 대해 알아두는 것이 좋다.

struct 모듈은 패킹된 바이트를 다양한 형의 필드로 구성된 튜플로 분석하고, 이와 반대로 튜플을 패킹된 바이트로 변환하는 함수를 제공한다. 

> https://python.flowdas.com/library/struct.html
>
> 파이썬 값과 파이썬 [`bytes`](https://python.flowdas.com/library/stdtypes.html#bytes) 객체로 표현되는 C 구조체 사이의 변환을 수행합니다.
>
> 다른 소스 중에서도, 파일에 저장되었거나 네트워크 연결에서 온 바이너리 데이터를 처리하는 데 사용할 수 있습니다.

클래스로 바이트 시퀀스를 생성하거나 저장할 수는 없지만, 바이트를 복사하지 않고 다른 이진 시퀀스, 패킹된 배열, 혹은 파이썬 이미징 라이브러리(PIL) 이미지 등 버퍼 데이터의 슬라이스에 공유 메모리 방식으로 접근할 수 있게 해준다.

> memoryview 객체는 C 수준 버퍼 인터페이스를 다른 객체와 마찬가지로 전달될 수 있는 파이썬 객체로 노출합니다.
>
> 외부의 자료를 읽어들일 때, 메모리에 복사 저장하는 것이 아닌 중간 단계인 버퍼에 저장합니다. 버퍼에 저장된 데이터를 수정할 수 있습니다. 

memoryview를 슬라이싱하면 바이트를 복사하지 않고 새로운 memoryview 객체를 반환한다. 



## 4.3 기본 인코더/디코더

> **코덱**([영어](https://ko.wikipedia.org/wiki/영어): codec)은 어떠한 [데이터](https://ko.wikipedia.org/wiki/자료) [스트림](https://ko.wikipedia.org/wiki/스트림_(컴퓨팅))이나 [신호](https://ko.wikipedia.org/wiki/신호)에 대해, [인코딩](https://ko.wikipedia.org/wiki/부호화)이나 [디코딩](https://ko.wikipedia.org/wiki/복호화), 혹은 둘 다를 할 수 있는 [하드웨어](https://ko.wikipedia.org/wiki/하드웨어)나 [소프트웨어](https://ko.wikipedia.org/wiki/소프트웨어)를 일컫는다. 또, 이를 위한 [알고리즘](https://ko.wikipedia.org/wiki/알고리즘)을 가리키는 용어로도 쓰인다.

코덱은 텍스트를 바이트로 혹은 바이트를 텍스트로 변환하기 위한 인코더/디코더이다. utf-8, utf-16 등 100여 개의 코덱을 제공한다. 

UTF 인코딩은 모든 유니코드 코드 포인트를 처리할 수 있게 만들어졌다. 



## 4.4 인코딩/디코딩 문제 이해하기

- 파이썬을 잘못 인코딩했을 때 발생하는 `UnicodeEncodedError`, `UnicodeDecodeError`, `SyntaxError`를 예방하거나 처리하는 방법

### 4.4.1 UnicodeEncodeError 처리하기

str을 이진 시퀀스로 변환할 때 발생 = 인코딩할 때

텍스트를 바이르토 변환할 때 문자가 대상 인코딩에 정의되어 있지 않으면, 
*인코딩 메서드나 함수의 errors 인수에 별도의 처리기를 지정하지 않으면 UnicodeEncodeError를 발생시킨다.* 

해결 방법: encoding() 메서드 errors 인자에 어떻게 처리할 것인지 선언해주거나, codecs.register_error() 메서드에 에러 처리기명과 에러처리 함수명을 전달해서 에러 처리기를 등록할 수 있다.

```python
'str'.encode('cp437', errors='ignore')

# 왜곡된 문자 그렘린(gremlin)(혹은 '문자 깨짐')를 다른 문자로 변환하여 보여줌 
'str'.encode('cp437', errors='replace')
```

### 4.4.2 UnicodeDecodeError 처리하기

이진 시퀀스를 텍스트로 변환할 때 정당한 문자로 변환할 수 없으면 발생

해결 방법: UnicodeEncodeError 처럼 잘못된 코덱ㄷ을 사용하면 쓰레기 문자를 조용히 디코딩하게 처리할 수 있다.

### 4.4.3 예상과 달리 인코딩된 모듈을 로딩할 때 발생하는 SyntaxError

Python 2.5부터는 아스키, Python3부터는 UTF-8을 소스 코드 기본 인코딩 방식으로 사용한다.

Python3에서 인코딩 선언 없이 비UTF-8로 인코딩된 .py 모듈을 로딩하면 SyntaxError가 발생한다.

윈도우 시스템은 UTF-8이 기본이 아니기 때문에 발생할 수 있다.

해결 방법: 파일 꼭대기에 coding 주석을 달아서 처리 `# coding: cp1252`



**비아스키 식별자의 사용에 대한 필자의 의견**

- 코드를 유지보수할 사람이 비아스키 문자로 구성된 자연어를 사용하고자 한다면 or Python2에서 코드를 실행할 필요가 없다면 자연어를 식별자로 사용하는 것도 좋다
- 전 세계 사람이 코드 개발에 참여한다면 아스키 문자로 구성된 영문 단어로 식별자를 만들어야 한다



4.4.4 &. 4.4.5 **메타데이터가 없을 때 인코딩 방식을 탐지하는 이론과 방법**

- 이론적으로는 불가능하지만 `Chardet` 패키지는 여러 주류 인코딩 방식에 대해 텍스트의 코덱을 상당히 잘 찾아낸다
- UTF-16, UTF-32에서 인코딩 방식을 알려주기 위해 사용하는 바이트 순서 표시

### 4.4.4. 바이트 시퀀스의 인코딩 방식을 알아내는 방법

바이트 시퀀스의 인코딩 방식을 알아내는 방법: 반드시 별도의 인코딩 정보를 가져와야 한다. 

통신 프로토콜이나 파일 포맷은 내용이 어떻게 인코딩되어 있는지 명시하는 헤더를 갖고 있다. 

어떤 패턴이 존재하거나/존재하지 않는다록해서 100% 어떤 인코딩으로 되어있는지 확신할 수 없다.

하지만 바이트 스트림이 자연어라 간주되면, 자연어에도 어떠한 규칙과 제한이 있다는 점을 고려하고 경험과 통계를 이용하여 인코딩 방식을 추정할 수 있다. Chardet은 이런 방법을 이용하여 30가지 인코딩 방식을 알아낸다. 

인코딩된 텍스트의 이진 시퀀스는 인코딩에 대한 정보를 명시적으로 전달하지 않지만, UTF 포맷은 텍스트 앞에 바이트 순서 표시(byte order mark)(BOM)을 추가할 수 있다.

### 4.4.5 BOM: 유용한 깨진 문자

```python
u16 = "El Niño".encode("utf-16")
b'\xff\xfeE\x00l\x00 \x00N\x00i\x00\xf1\x00o\x00'
```

`b'\xff\xfe` 이 문자가 바이트 순서 표시(BOM)로, 인코딩한 인텔 CPU의 리틀엔디언(little endian) 바이트 순서를 나타낸다.

> https://genesis8.tistory.com/37
>
> 엔디언은 컴퓨터의 메모리와 같은 1차원의 공간에 여러 개의 연속된 대상을 배열하는 방법을 뜻하며, 바이트를 배열하는 방법을 바이트 순서(byte order)라 한다.
>
> https://rakuraku.tistory.com/96

```python
list(u16)
Out[39]: [255, 254, 69, 0, 108, 0, 32, 0, 78, 0, 105, 0, 241, 0, 111, 0]
```

리틀엔디언 컴퓨터에서는 코드 포인트의 최하위 바이트가 먼저 나온다. 

UTF-16 인코딩은 ZERO WIDTH NO-BREAK SPACE(U+FEFF)라는 특수문자를 인코딩된 텍스트 앞에 붙이는데, `b'\xff\xfe` 이 문자는 화면에 출력되지 않는다. 코덱은 어떤 바이트 순서를 사용해야 할지 알 수 잇다. 

ZERO WIDTH NO-BREAK SPACE(U+FEFF)를 제외하고 코드 포인트의 최하위 바이트가 먼저 나온다 → 69, 0



## 4.5 텍스트 파일 다루기

텍스트를 처리하는 최고의 방법은 유니코드 샌드위치다.

![Python's bytes and strings](/Users/sunho/repos/WIL/images/Python's bytes and strings.png)

파일을 열자마자 str으로 바꾸고, 모든 처리가 끝날 때까지 bytes로 인코딩하지 말라.

텍스트 파일을 열 때 encoding 인수가 필수는 아니지만 반드시 사용하는 것이 좋다
특히 윈도우에서 읽고 쓸 때 모두 인코딩을 지정해주는 것이 좋다. 

텍스트 파일을 이진 모드로 열지 않는 것이 좋다. 

텍스트 파일 열기 연산

### 4.5.1 기본 인코딩 설정: 정신 나간 거 아냐?

GNU/리눅스 및 OS X에서는 수년간 기본적으로 모든 인코딩이 UTF-8로 설정되어 있었으므로 모든 입출력 루틴이 유니코드 문자를 다룬다.



윈도우에서는 동일한 시스템 안에 여러 인코디이 사용될 뿐만 아니라 아스키 및 인코딩에 따라 달라지는 추가 127개의 문자만 지원하는 cp850이나 cp1252 같은 코드 페이지가 일반적으로 사용된다. 

따라서 윈도우 사용자는 특별한 주의를 기울이지 않으면 인코딩 오류를 접할 가능성이 훨씬크다.

윈도우의 경우 안타깝게도 동일 컴퓨터 안에서도 이 설정들이 서로 다른 값을 가지며, 서로 호환되지 않는 경우도 있다.



파이썬 기본 인코딩 방식을 알아내기 위해 사용하는 locale.getpreferredencoding(), sys.getfilesystemencodint(), sys.getdefaultencoding() 메서드 및 표준 입출력 파일에 대한 인코딩(sys.stdout.encoding 등)에 대해 설명했다.

locale.getpreferredencoding() 함수가 반환하는 설정이 가장 중요한 인코딩 설정이다. 텍스트 파일을 열 때, 표준 입출력을 리다이렉션할 때 기본적으로 사용된다.
제일 좋은건 '기본 인코딩에 의존 하지 않는 것'이 가장 좋다.



## 4.6 제대로 비교하기 위해 유니코드 정규화하기

발음 구별 기호가 있는 문자열의 경우 출력할 경우 같아 보이지만 서로 다른 두 개의 코드 포인트 시퀀스일 경우 동일하지 않다고 판단한다

이 문제를 해결하려면 unicodedata.normalize() 함수가 제공하는 유니코드 정규화를 이용해야 한다. 정규화 방식(Normalization Form) NFC, NFD, NFKC, NFKD.. 

유니코드는 동일 문자를 여러 방식으로 표현할 수 있으므로 문자열 비교가 의외로 복잡하다. 따라서 텍스트를 매칭하려면 반드시 문자열을 정규화해야 한다.

- 정규화와 케이스 폴딩 외에도 악센트를 모두 제거하는 방법 등 필요에 따라 텍스트를 상당히 많이 변환하는 유틸리티 함수도 몇 가지 살펴보았다.



### 4.6.3 극단적인 '정규화': 발음 구별 기호 제거하기

극단적인 방법.. URL에 적용하면 보기엔 편하지만.. 원래 의미를 변경할 가능성이 높다.

대상 언어, 사용자, 변환된 텍스트의 사용법에 대해 잘 알고 있는 사람이 이렇게 깊게 변환해야 할지 여부를 판단하는 것이 좋겠다.



## 4.7 유니코드 텍스트 정렬

파이썬에서 비아스키 텍스트는 locale.strxfrm() 함수를 이용해서 변환하는 것이 표준이다. 

하지만 호출전에 setlocale(LC_COLLATE, <지역언어>)를 호출해야 하는데, 지역 설정을 시스템 전역에 영향을 미치므로 호출하는 것을 권장하지 않는다.

그래서 `PyUCA` 라이브러리를 활용하여 간단히 해결할 수 있다.



## 4.8 유니코드 데이터베이스

유니코드 데이터베이스는 모든 문자에 대한 메타데이터를 제공해준다.



## 4.9 이중 모드 str 및 bytes API

이중 모드 API는 re 및 os 모듈처럼 str이나 bytes 인수를 모두 받을 수 있으며, 인수의 종류에 따라 적절히 처리하는 함수를 제공한다.

표준 라이브러리인 re와 os 등은 str이나 bytes 인수를 모두 받으며, 인수의 자료형에 따라 다르게 작동한다. = 이중 모드 

### 4.9.1 정규 표현식에서의 str과 bytes

bytes 패턴은 문자와 숫자에 대한 아스키 바이트에만 매칭된다. 아스키 범위를 벗어나는 문자들은 숫자나 단어로 처리하지 않는다.

str 패턴은 문자, 위첨자, 타밀, 아스키 숫자에 매칭된다. 아스키 문자에만 매칭하게 만드는 re.ASCII 플래그가 있다.

