# Big decimal

### For C++, API to overcome epsilon problem in big decimal computation

> 이 API는, 최대 int사이즈 길이(한계가 거의 없는)의 숫자 형식의 문자열을 사칙연산합니다.
float(double) 타입의 사칙연산(음수,양수)에서 발생되는 엡실론 문제를 극복하기 위해서, 만들어진 API(클래스)입니다.
많은 수의 단위테스트가 필요했고, 이를 위해서 구글 gtest를 사용했고, 해당 라이브러리가 필요합니다.

#### 몇가지 주의할 사항 및 고려할 사항이 있습니다.
1. Bigdecimal 설계상. 불변객체 지향합니다. 즉, 생성자 호출시에 초기화를 하고, 이후, 객체 내부 상태변경을 불가능하도록 설계하였습니다.
2. 1을 지키기 위해, 생성자 호출시, input값을 받아야만 합니다. 그래서 기본생성자는 호출금지로 설계되었습니다.
3. 1을 지키기 위해, 불가피하게 생성자호출시 input값을 초기화를 위해서 init() 이라는 내부함수를 호출하는데, 이 함수는 간단하지 않습니다.
   c++에서 생성자 호출시. 메모리 배정 외에, 익셉션을 호출될지 모르는 함수를 호출하는 방식은 위험한 설계입니다.
   위의 문제를 극복하는 여러가지가 있으나, 그 부분은 각자 성향에 맞게 수정하십시요.
4. cmake 버전 3.14이상이면 단위테스트 가능합니다. 클래스만 복사해서 사용도 가능합니다.
5. 최대 복잡도는 O<sup>2</sup> + nO 입니다. 그래서 성능상 문제가 있습니다.
   그러나, 대부분 케이스에서 엄청난 큰수를 계산하지 않을 거라고 판단됩니다.
6. Bigdecimal.h 파일에 MAX_DECIMAL_POINT enum타입 상수가 10으로 셋팅되어 있습니다. 
   사칙연산시, 최대 표현 가능한 소수부는 10자리(Limit)의미합니다. 한계치를 정하세요. 
   무한루프에 빠질 수 있습니다. 이 부분도 성향에 맞게 수정하십시요.
7. 버그가 있을 경우, bug branch를 생성 후 commit 하시면, 최대한 빠른 시일안에 수정 및 재배포하겠습니다.

#### 단위테스트 아래의 방식으로 수행가능합니다.
1. 구글 gtest 설치(Debian 계열).
```shell
sudo apt install libgtest-dev
```
2. cmake로 make파일 생성
```shell
cmake CMakeList.txt
```
3. make 실행
```shell
make
```
4. main 바이너리 실행(단위테스트 실행)
```shell
./main
```

---------------------------
#### 사용법
1. 비교연산자
```c++
if( Bigdecimal{"0"} > Bigdecimal{"-99999999999999999999999999999999999999999999999999999999999999999999"} )
    std::cout << "true"<< std::endl;

if( Bigdecimal{"1237.001929345"} < Bigdecimal{"0.1"} )
    std::cout << "not happening"<< std::endl;
else
    std::cout << "false"<< std::endl;

if( Bigdecimal{"1237.001929345"} <= Bigdecimal{"1237.001929344"} )
    std::cout << "not happening"<< std::endl;
else
    std::cout << "false"<< std::endl;

if( Bigdecimal{"1237.001929345"} >= Bigdecimal{"1237.001929344"} )
    std::cout << "true"<< std::endl;
else
    std::cout << "not happening"<< std::endl;
```
2. 더하기 연산자
```c++
Bigdecimal a{"99291929394"};
Bigdecimal b{"99291929394"};
Bigdecimal c = a + b;
std::cout << c << std::endl; //출력 198583858788
```
3. 빼기 연산자
```c++
Bigdecimal a{"1.0000001"};
Bigdecimal b{"1.9"};
Bigdecimal c = a - b;
std::cout << c << std::endl; //출력 -0.8999999
```
4. 곱하기 연산자
```c++
Bigdecimal a{"9123912938129382934829348239"};
Bigdecimal b{"912389348394894584958"};
Bigdecimal c = a * b;
std::cout << c << std::endl; //출력 8324560980431615848446804538000241607397553188962
```
5. 나누기 연산자
```c++
Bigdecimal a{"8324560980431615848446804538.000241607397553188962"};
Bigdecimal b{"0.912389348394894584958"};
Bigdecimal c = a / b;  
std::cout << c << std::endl;  //출력 9123912938129382934829348239
```
6. 기타
```c++
Bigdecimal a{"-0.00000000"};
std::cout<< a.to_String() << std::endl; //출력 0

Bigdecimal b{"      0.00000000"};
std::cout<< b.to_String() << std::endl; //출력 0

Bigdecimal c{"      +0.00000000000000000000000000000000000000"};
std::cout<< c.to_String() << std::endl; //출력 0

Bigdecimal d{"      -0.0"};
std::cout<< d.to_String() << std::endl; //출력 0

Bigdecimal e{"034"};
std::cout<< d.to_String() << std::endl; //출력 34

Bigdecimal f{"100.0000"};
std::cout<< f.to_String() << std::endl; //출력 100

Bigdecimal g{"1          "}; //뒤에 공백이 있을 경우 허용안됨. runtime_error exception 발생
Bigdecimal h{"kejake123545"}; //숫자가 아닌 문자값은 허용안됨. runtime_error exception 발생
```