<h1> gcc와 makefile에 대한 정리<h1>

<h2>gcc</h2>

<h3>기본적인 컴파일 하는 방법</h3>

> gcc <소스 파일이름>

```c
#include<stdio.h>
int main()
{
    prnitf("Hello Linux!");
    return 0;
}
```

예시 : **$ gcc hello.c**

컴파일 후 파일을 실행시키려 할 때 다음과 같이 하면 안된다.   

**$ hello**   
bash: hello: command not found

현재 hello.c가 있는 파일에   
**$ ls**   
명령어를 입력해보면 다음과 같이 **a.out**파일이 생성되었음을 볼 수 있다.   
즉, 아무 옵션 없이 컴파일을 하게 된다면 **a.out**실행 파일이 만들어지게 됨을 알 수 있다.



그렇다면, 이 파일을 실행시키기 위해서   
**$ a.out**   
명령만 입력을 하게되면 실행이 될까?

이 또한 오류가 발생하게 되는데 그 이유는 다음과 같다.   

> 유닉스는 기본적으로 PATH 라는 환경변수에 있는 디렉토리에서만 실행화일을 찾을 뿐입니다.   
>  만약 PATH 라는 환경변수에 현재 디렉토리를 의미하는 도트 문자(.)가 들어있지 않으면 현재 디렉토리의 실행화일은 절대 실행되지 않습니다.   
>  게다가 현재 디렉토리를 PATH 환경 변수에 넣어준다 할 지라도 도스처럼 현재 디렉토리를 먼저 찾는다든지 하는 일은 없습니다.   
>  오로지 PATH 에 지정한 순서대로 수행합니다.

<br>
그러므로 파일을 실행하려면   
**$ ./a.out**   
다음과 같이 입력을 해주어서 실행을 해야 한다.
<br><br>
<h3> -o 옵션</h3>

> gcc -o <실행파일 이름> <소스파일 이름>

예시  : **$ gcc -o hello hello.c**

-o 옵션은 output file 이름을 정하는 옵션이다.   
그래서 위의 예시를 입력하게 되면 **a.out**파일 대신 **hello**라는 녹색의 파일이 생성됨을 알 수 있다.   
<br><br>
<h3>-c 옵션</h3>

> gcc -c <소스파일 이름>   
   
예시 : **$ gcc -c hello.c**

-c 옵션은 오로지 컴파일 작업만 실시하고 싶을 때 사용하는 옵션이다.    
컴파일을 하고 나면 만들어지는 파일은 .o파일이 만들어지는데 이는 object file이라고 한다.   

여기서 중요하게 알아야 할 것은 **gcc는 컴파일러가 아니다**라는 것이다.   
gcc는 컴파일러가 아니라 c컴파일러를 돌리는 녀석이라고 한다.

즉, 컴파일(.c ->.o)과정을 거친 후 링크(.o->실행파일 (a.out))을 만들어 준다.  

즉, gcc는 컴파일러와 링커를 불러주는 대리인이라고 할 수 있다.

<br><br>
<h2>Makefile</h2>
<h3>make</h3>
프로젝트를 진행할 때 소스파일이 너무 많아지게 되면 일일이 gcc 명령을 이용해 처리하는 것은 매우 힘든 일이다.   
그래서 하나의 프로젝트를 효율적으로 관리하고 일관성있게 관리하기 위해서 **Makefile**이라는 형식을 사용하고 **make**라는 유틸리티를 이용한다.

이는 현재 dir에 makefile이라는 일정한 규칙을 준수하여 만든 file의 내용을 읽어서 목표 file(target)을 만들어 낸다.


foo.c
```c
extern void bar ( void );

int
main ( void )
{
  bar ();
  return 0;
}
```
bar.c
```c
#include <stdio.h>

void
bar ( void )
{
  printf ( "DRRRRRRrrrrr\n" );
}
```

> **$ gcc -o foo foo.c bar.c**

위의 명령은 foo라는 실행파일을 foo.c , bar.c라는 2개의 소스로부터 만들었다.

이러한 명령을 Makefile을 이용해서 만들어보자.

현재 폴더에 Makefile을 만들고 다음과 같이 입력을 한다.

Makefile의 형식은 다음과 같다.
```
target : list-of-dependencies<br>
    command-to-generate-target
```
예시 파일 참고
```makefile
 foo:   foo.o bar.o 
        gcc -o foo foo.o bar.o

 foo.o: foo.c
        gcc -c foo.c

 bar.o: bar.c
        gcc -c bar.c
```

여기서 가장 중요한 것은 target에 해당하는 줄의 다음 줄에서는 <탭>키를 사용해야 한다!!!   
띄어쓰기를 이용해서 사용하면 안된다는 점을 꼭 기억해야 한다!!


>**$ make**   
>**$ ./foo**

다음을 입력하면 **foo** 실행파일이 생성되어 실행되는 것을 알 수 있다.

```makefile
 foo:   foo.o bar.o 
        gcc -o foo foo.o bar.o

 foo.o: foo.c
        gcc -c foo.c

 bar.o: bar.c
        gcc -c bar.c

clean:
        rm -f foo foo.o bar.o
```

makefile을 이용해 해당 파일들을 지우기 위해서는 makefile안에 clean target을 생성하면 된다.   

clean은 단지 file들을 지워주는역할을 한다는 것을 알면 된다.

```makefile
 all:   foo

 foo:   foo.o bar.o 
        gcc -o foo foo.o bar.o

 foo.o: foo.c
        gcc -c foo.c

 bar.o: bar.c
        gcc -c bar.c

clean:
        rm -f foo foo.o bar.o
```

이번에는 **all**이라는 target을 만들어서 작업을 진행했다.   
즉 모든 것들이 한번에 만들어짐을 알 수 있다.

<h3>Macro</h3>

makefile을 쉽게 만들기 위해 사용되는 변수를 macro라 한다.   

```makefile
 OBJECTS = foo.o bar.o

 all:   foo

 foo:   $(OBJECTS)
        gcc -o $@ $(OBJECTS)

 foo.o: foo.c
        gcc -c foo.c

 bar.o: bar.c
        gcc -c bar.c

clean:
        rm -f foo foo.o bar.o
```

여기서 알 수 있는 점은 2가지이다.

첫번째로 OBJECTS를 c언어에서 #DEFINE 과 동일하게 사용되었다는 점이다.

두번째로 $@는 실행파일의 이름을 target과 동일하게 만든다는 것이다.

>참고 : <br>
 >> \$<     입력 화일을 의미합니다. 콜론의 오른쪽에 오는 패턴을 치환합니다.<br>
 >> \$@     출력 화일을 의미합니다. 콜론의 왼쪽에 오는 패턴을 치환합니다.<br>
 >> \$*     입력 화일에서 꼬리말(.c, .s 등)을 떼넨 화일명을 나타냅니다.<br>


 참고자료 : http://doc.kldp.org/KoreanDoc/html/gcc_and_make/gcc_and_make-3.html#ss3.1