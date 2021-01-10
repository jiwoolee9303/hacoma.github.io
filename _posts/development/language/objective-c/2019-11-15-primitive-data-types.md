---
title: "[Objective-C] 원시 데이터형 - Primitive Data Types"
last_modified_at: 2019-11-15T13:12+09:00
header:
  overlay_color: "#333"
categories:
  - Development/Language/Objective-C
tags:
  - Language
  - Objective-C
---

# 주의

본 글은 `Objective-C 2.0` 기준으로 작성되었다.

# 들어가며

Objective-C를 처음 공부할 때는 Foundation에서 제공하는 Class나 Type Alias로 재정의된 자료형을 엄격히 구분하여 공부하는 것이 좋다고 생각한다. 이번 포스팅에서는 Primitive Data Types에 대하여 정리해보도록 하겠다.

Objective-C는 ANSI C의 `strict  superset`으로 ANSI C에서 제공하는 대부분의 데이터 타입뿐만 아니라 구조체(struct)와 공용체(union)도 사용할 수 있다.

# 한번 알아보자

## 1. 문자 데이터형

### 1) char (32bit / 64bit)

|TYPE|크기|표현범위|
|---|---|---|
|signed char|1byte|Min : -128<br/>Max : 127|
|unsigned char|1byte|Min : 0<br/>Max : 255|

## 2. 정수 데이터형

### 1) short int (32bit / 64bit)

|TYPE|크기|표현범위|
|---|---|---|
|signed short int|2byte|Min : -32,768<br/>Max : 32,767|
|unsigned short int|2byte|Min : 0<br/>Max : 65,535|

### 2) int (32bit / 64bit)

|TYPE|크기|표현범위|
|---|---|---|
|signed int|4byte|Min : -2,147,483,648<br/>Max : 2,147,483,647|
|unsigned int|4byte|Min : 0<br/>Max : 4,294,967,295|

### 3) long int (32bit)

|TYPE|크기|표현범위|
|---|---|---|
|signed long int|4byte|Min : -2,147,483,648<br/>Max : 2,147,483,647|
|unsigned long int|4byte|Min : 0<br/>Max : 4,294,967,295|

### 4) long int (64bit)

|TYPE|크기|표현범위|
|---|---|---|
|signed long int|8byte|Min : -9,223,372,036,854,775,808<br/>Max : 9,223,372,036,854,775,807|
|unsigned long int|8byte|Min : 0<br/>Max : 18,446,744,073,709,551,615|

### 5) long long int (32bit / 64bit)

|TYPE|크기|표현범위|
|---|---|---|
|signed long long int|8byte|Min : -9,223,372,036,854,775,808<br/>Max : 9,223,372,036,854,775,807|
|unsigned long long int|8byte|Min : 0<br/>Max : 18,446,744,073,709,551,615|

## 3. 부동 소수 데이터형

### 1) float (32bit / 64bit)

|TYPE|크기|표현범위|
|---|---|---|
|float|4byte|Min : 1.175494e-38<br/>Max : 3.402823e+38|

### 2) double (32bit / 64bit)

|TYPE|크기|표현범위|
|---|---|---|
|double|8byte|Min : 2.225074e-308<br/>Max : 1.797693e+308|

## 4. 논리 데이터형

### 1) BOOL

논리 값을 나타내는 것에 사용되는 BOOL은 사실 typedef로 재정의된 데이터형이다.
컴파일러의 종류나 버전 혹은 바이너리가 실행되는 플랫폼 환경에 따라서 `int` 혹은 `char`가 재정의된 형태이다.
C 언어에서는 C99 이전에 BOOL 데이터형을 개발자들이 아래와 같이 직접 정의해서 사용했다.

```objectivec
typedef int bool

#define false 0
#define true  1

bool bValue;
bValue = true;
```

그러다가 C99에서 BOOL 데이터형이 정식 도입되었다. 위 예시처럼 개발자들이 이미 C99 이전에 int 데이터형을 bool로 재정의해서 사용하고 있었기 때문에 bool이라는 키워드로는 지원하지 못하고 `_BOOL`이라는 키워드로 논리 값을 표현하는 데이터형을 지원하였다.

```objectivec
// stdbool.h

typedef _Bool bool;

#define true   (_Bool)1
#define false  (_Bool)0
```

그러나 `_BOOL`은 `bool`에 비해 조금 길다보니까 위에서 볼 수 있듯이 `stdbool.h`에서 별도로 `bool` 데이터형을 정의하여 제공했다. 그래서 C99에서 `_BOOL` 대신 `bool`을 사용하고 싶다면 `stdbool.h`를 include 해서 사용해야 한다.

근데 우리가 지금 보고 있는 것은 Objective-C인데 왜 C 언어 이야기를 하나 의아할 것이다. 게시글의 도입부에서 말했던 것처럼 Objective-C는 C 언어의 `strict superset`이기 때문에
Objective-C 역시 BOOL은 컴파일러의 종류나 버전 혹은 플랫폼 환경에 따라서 원본 데이터형이 `int`가 될 수도 있고 `char`가 될 수도 있다.

궁금하니까 직접 확인해보자.  
그럼 먼저 macOS SDK와 iOS SDK에 있는 `objc.h`를 까보자.

```objectivec
// objc.h

/// Type to represent a boolean value.

#if defined(__OBJC_BOOL_IS_BOOL)
    // Honor __OBJC_BOOL_IS_BOOL when available.
#   if __OBJC_BOOL_IS_BOOL
#       define OBJC_BOOL_IS_BOOL 1
#   else
#       define OBJC_BOOL_IS_BOOL 0
#   endif
#else
    // __OBJC_BOOL_IS_BOOL not set.
#   if TARGET_OS_OSX || TARGET_OS_MACCATALYST || ((TARGET_OS_IOS || 0) && !__LP64__ && !__ARM_ARCH_7K)
#      define OBJC_BOOL_IS_BOOL 0
#   else
#      define OBJC_BOOL_IS_BOOL 1
#   endif
#endif

#if OBJC_BOOL_IS_BOOL
    typedef bool BOOL;
#else
#   define OBJC_BOOL_IS_CHAR 1
    typedef signed char BOOL; 
    // BOOL is explicitly signed so @encode(BOOL) == "c" rather than "C" 
    // even if -funsigned-char is used.
#endif
```

와우! 헤더에서 BOOL 관련된 내용만 발췌해왔는데도 복잡하다.  
그래도 하나씩 살펴보겠다.

#### (1) 결정 조건 1

```objectivec
#if defined(__OBJC_BOOL_IS_BOOL)
    // Honor __OBJC_BOOL_IS_BOOL when available.
#   if __OBJC_BOOL_IS_BOOL
#       define OBJC_BOOL_IS_BOOL 1
#   else
#       define OBJC_BOOL_IS_BOOL 0
#   endif
#else
```

`__OBJC_BOOL_IS_BOOL`이 정의되어 있을 때는 이 값에 따라서 `OBJC_BOOL_IS_BOOL`을 `0` 혹은 `1`로 정의한다.

#### (2) 결정 조건 2

```objectivec
#else
    // __OBJC_BOOL_IS_BOOL not set.
#   if TARGET_OS_OSX || TARGET_OS_MACCATALYST || ((TARGET_OS_IOS || 0) && !__LP64__ && !__ARM_ARCH_7K)
#      define OBJC_BOOL_IS_BOOL 0
#   else
#      define OBJC_BOOL_IS_BOOL 1
#   endif
#endif
```

`__OBJC_BOOL_IS_BOOL`이 정의되어 있지 않을 때는 타겟 운영체제와 플랫폼, 아키텍처 종류에 따라서 `OBJC_BOOL_IS_BOOL`을 `0` 혹은 `1`로 정의한다.

#### (3) 결정 조건 3

```objectivec
#if OBJC_BOOL_IS_BOOL
    typedef bool BOOL;
#else
#   define OBJC_BOOL_IS_CHAR 1
    typedef signed char BOOL; 
    // BOOL is explicitly signed so @encode(BOOL) == "c" rather than "C" 
    // even if -funsigned-char is used.
#endif
```

**결정 조건 1**과 **결정 조건 2**에 따라 결정된 `OBJC_BOOL_IS_BOOL`가 참이면 bool을 BOOL로 재정의, 거짓이면 signed char을 BOOL로 재정의한다.

#### (4) 결정 조건이 맞는지 테스트 해보자
오오! 헤더를 까보고 나니까 대충 알겠다. 그럼 코드를 짜서 디버깅해보면서 확인해보자.
먼저 macOS Command Line Tool 프로젝트를 생성하고 아래와 같이 코드를 작성해보겠다.

```objectivec
NSLog(@"__OBJC_BOOL_IS_BOOL = %@", __OBJC_BOOL_IS_BOOL ? @"defined" : @"undefined");
NSLog(@"TARGET_OS_OSX = %@", TARGET_OS_OSX ? @"YES" : @"NO");
NSLog(@"OBJC_BOOL_IS_BOOL = %@", OBJC_BOOL_IS_BOOL ? @"YES" : @"NO");
NSLog(@"OBJC_BOOL_IS_CHAR = %@", OBJC_BOOL_IS_CHAR ? @"YES" : @"NO");
```

결과는 다음과 같이 나왔다.

```bash
__OBJC_BOOL_IS_BOOL = undefined
TARGET_OS_OSX = YES
OBJC_BOOL_IS_BOOL = NO
OBJC_BOOL_IS_CHAR = YES
```

앞서 정리했던 세 가지의 결정 조건을 보면서 출력 결과를 이해해보면 아래와 같이 말해볼 수 있을 것 같다.

"Xcode 11.5, macOS 10.15 SDK로 빌드한 macOS Command Line Tool을 x86_64, macOS Catalina 10.15.4 환경에서 돌렸을 때 BOOL은 char를 재정의한 데이터형이다."

그럼 iOS 기기에서도 테스트 해보자. iOS Single View App 프로젝트를 생성하고 아래와 같이 코드를 작성해보겠다.

```objectivec
NSLog(@"__OBJC_BOOL_IS_BOOL = %@", __OBJC_BOOL_IS_BOOL ? @"defined" : @"undefined");
NSLog(@"TARGET_OS_OSX = %@", TARGET_OS_OSX ? @"YES" : @"NO");
NSLog(@"OBJC_BOOL_IS_BOOL = %@", OBJC_BOOL_IS_BOOL ? @"YES" : @"NO");
```

결과는 다음과 같이 나왔다.

```bash
__OBJC_BOOL_IS_BOOL = defined
TARGET_OS_OSX = NO
OBJC_BOOL_IS_BOOL = YES
```

아! 그럼 이건 또 아래와 같이 말해볼 수 있겠다.

"Xcode 11.5, iOS 13.5 SDK로 빌드한 iOS Single View App을 iPhone 11 Pro, arm64, iOS 13.5.1 환경에서 돌렸을 때 BOOL은 int를 재정의한 데이터형이다.
왜냐하면 BOOL은 bool을 재정의한 데이터형이고 bool은 int를 재정의한 데이터형이기 때문이다."

땡, 아니였다. 내가 마지막으로 사용했던 C 언어 개발 환경에서 bool은 int라서 Objective-C에서도 당연히 int일 줄 알았는데 char 데이터형이였다.
처음에는 sizeof를 찍었을 때 1이 나와서 어리둥절했는데 [애플 개발자 문서](https://developer.apple.com/documentation/objectivec/bool)를 찾아보니
이에 대한 내용들이 잘 나와있었다 ㅎㅎ

결국 BOOL은 아래의 표로 표현할 수 있을 것 같다.

|TYPE|Typedef|참|거짓|
|---|---|---|---|
|BOOL|int 또는 signed char|YES|NO|

#### (5) 팁 : processor architecture 확인 방법

리눅스에서 하던대로 processor architecture를 확인해보려고 아래와 같은 커맨드를 수행했다.

```bash
> arch
i386
```

결과는 `i386`이 출력되었다. 아닙니다 선생님 그럴 리가 없습니다 제 맥은 64비트인 것으로 알고 있는데 이게 어떻게 된 일입니까?
식겁하고 구글링 좀 해봤다.

- [https://unix.stackexchange.com/questions/518318/what-does-i386-mean-on-macos-mojave](https://unix.stackexchange.com/questions/518318/what-does-i386-mean-on-macos-mojave)
- [https://apple.stackexchange.com/questions/140651/why-does-arch-output-i386](https://apple.stackexchange.com/questions/140651/why-does-arch-output-i386)

아하~ 맥에서 `arch`는 PowerPC인지 Intel CPU인지 여부에 따라 Intel CPU면 무조건 `i386`을 돌려준다고 한다. 고로 `i386`이 나오면 `2-way universal` 혹은 `32-bit only` 둘 중 하나라는 것이다.

그럼 machine hardward name을 찍어보자. 아래와 같은 커맨드를 수행하면 된다.

```bash
> machine
x86_64h
```

혹은

```bash
> uname -m
x86_64
```

암요. 그럴 리가 없지요. 편안~

# 눈으로 직접 확인을 해보자

이제 각 데이터형의 사이즈와 최소값, 최대값을 직접 확인해보자.  
나는 32bit 기기를 가지고 있지 않아서 64bit 기기에서만 찍어보겠다.

## 1. 먼저 각 데이터형의 사이즈부터 보자

### 1) 문자 데이터형

#### (1) char (64bit)

```objectivec
NSLog(@"size of char is %ld", sizeof(char));
NSLog(@"size of unsigned char is %ld", sizeof(unsigned char));
```

```bash
size of char is 1
size of unsigned char is 1
Program ended with exit code: 0
```

### 2) 정수 데이터형

#### (1) short int (64bit)

```objectivec
NSLog(@"size of short int is %ld", sizeof(short int));
NSLog(@"size of unsigned short int is %ld", sizeof(unsigned short int));
```

```bash
size of short int is 2
size of unsigned short int is 2
Program ended with exit code: 0
```

#### (2) int (64bit)

```objectivec
NSLog(@"size of int is %ld", sizeof(int));
NSLog(@"size of unsigned int is %ld", sizeof(unsigned int));
```

```bash
size of int is 4
size of unsigned int is 4
Program ended with exit code: 0
```

#### (3) long int (64bit)

```objectivec
NSLog(@"size of long int is %ld", sizeof(long int));
NSLog(@"size of unsigned long int is %ld", sizeof(unsigned long int));
```

```bash
size of long int is 8
size of unsigned long int is 8
Program ended with exit code: 0
```

#### (4) long long int (64bit)

```objectivec
NSLog(@"size of long long int is %ld", sizeof(long long int));
NSLog(@"size of unsigned long long int is %ld", sizeof(unsigned long long int));
```

```bash
size of long long int is 8
size of unsigned long long int is 8
Program ended with exit code: 0
```

### 3) 부동 소수 데이터형

#### (1) float (64bit)

```objectivec
NSLog(@"size of float is %ld", sizeof(float));
```

```bash
size of float is 4
Program ended with exit code: 0
```

#### (2) double (64bit)

```objectivec
NSLog(@"size of double is %ld", sizeof(double));
```

```bash
size of double is 8
Program ended with exit code: 0
```

### 4) 논리 데이터형

#### (1) BOOL (64bit)

```objectivec
NSLog(@"size of BOOL is %ld", sizeof(BOOL));
```

```bash
size of BOOL is 1
Program ended with exit code: 0
```

## 2. 다음은 각 데이터형의 최소값과 최대값을 찍어보자

### 1) 문자 데이터형

#### (1) char (64bit)

```objectivec
NSLog(@"CHAR MIN is %d, CHAR MAX is %d", CHAR_MIN, CHAR_MAX);
NSLog(@"UCHAR MAX is %u", UCHAR_MAX);
```

```bash
CHAR MIN is -128, CHAR MAX is 127
UCHAR MAX is 255
Program ended with exit code: 0
```

### 2) 정수 데이터형

#### (1) short int (64bit)

```objectivec
NSLog(@"SHRT MIN is %hd, SHRT MAX is %hd", SHRT_MIN, SHRT_MAX);
NSLog(@"USHRT MAX is %hu", USHRT_MAX);
```

```bash
SHRT MIN is -32768, SHRT MAX is 32767
USHRT MAX is 65535
Program ended with exit code: 0
```

#### (2) int (64bit)

```objectivec
NSLog(@"INT MIN is %d, INT MAX is %d", INT_MIN, INT_MAX);
NSLog(@"UINT MAX is %u", UINT_MAX);
```

```bash
INT MIN is -2147483648, INT MAX is 2147483647
UINT MAX is 4294967295
Program ended with exit code: 0
```

#### (3) long int (64bit)

```objectivec
NSLog(@"LONG MIN is %ld, LONG MAX is %ld", LONG_MIN, LONG_MAX);
NSLog(@"ULONG MAX is %lu", ULONG_MAX);
```

```bash
LONG MIN is -9223372036854775808, LONG MAX is 9223372036854775807
ULONG MAX is 18446744073709551615
Program ended with exit code: 0
```

#### (4) long long int (64bit)

```objectivec
NSLog(@"LLONG MIN is %lld, LLONG MAX is %lld", LLONG_MIN, LLONG_MAX);
NSLog(@"ULLONG MAX is %llu", ULLONG_MAX);
```

```bash
LLONG MIN is -9223372036854775808, LLONG MAX is 9223372036854775807
ULLONG MAX is 18446744073709551615
Program ended with exit code: 0
```

### 3) 부동 소수 데이터형

#### (1) float (64bit)

```objectivec
NSLog(@"FLT MIN is %f, FLT MAX is %f", FLT_MIN, FLT_MAX);
```

```bash
FLT MIN is 0.000000, FLT MAX is 340282346638528859811704183484516925440.000000
Program ended with exit code: 0
```

#### (2) double (64bit)

```objectivec
NSLog(@"DBL MIN is %f, DBL MAX is %f", DBL_MIN, DBL_MAX);
```

```bash
DBL MIN is 0.000000, DBL MAX is 179769313486231570814527423731704356798070567525844996598917476803157260780028538760589558632766878171540458953514382464234321326889464182768467546703537516986049910576551282076245490090389328944075868508455133942304583236903222948165808559332123348274797826204144723168738177180919299881250404026184124858368.000000
Program ended with exit code: 0
```

지금까지 Objective-C에서 제공하는 원시 데이터형을 알아봤다. 나처럼 공부할 때 밑바닥을 봐야하는 성격인 사람들은 "NSInteger와 int의 차이는 뭐야?", "CGFloat와 float 차이는 뭐야?"
라는 궁금증이 더 생길 수도 있다. 이건 직접 헤더를 까보면서 확인해보자. 헤더는 명세 그 자체이다.

비록 실제 개발할 때는 아키텍처 환경이나 플랫폼에 따라서 적절한 데이터형을 typedef로 재정의해놓은 자료형들을 주로 사용할 것이라 생각한다.
하지만 원시 데이터형을 잘 알고 있는 것이 언젠가는 도움이 되는 순간이 올 것이라고 생각한다.

# 참조

- [https://code.tutsplus.com/tutorials/objective-c-succinctly-data-types–mobile-21986](https://code.tutsplus.com/tutorials/objective-c-succinctly-data-types–mobile-21986)
- [https://andybargh.com/primitive-data-types-objective-c/](https://andybargh.com/primitive-data-types-objective-c/)
- [https://www.tutorialspoint.com/objective_c/objective_c_data_types.htm](https://www.tutorialspoint.com/objective_c/objective_c_data_types.htm)

===

부족한 글 읽어주셔서 감사합니다.

잘못된 내용이나 오탈자에 대한 지적은 언제나 환영입니다.  
댓글로 남겨주시면 반영하도록 하겠습니다.