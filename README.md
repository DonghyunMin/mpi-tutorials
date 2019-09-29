## MPI 프로그래밍 튜토리얼 및 정리 레포지토리
본 저장소는 분산병렬컴퓨팅 강의 중 MPI 프로그래밍 내용을 정리하기 위해 개설된 공간입니다. MPI 라이브러리의 기본적인 설치 방법 및 사용방법을 구글 통해서 찾아보려고 했으나 영문 및 한글로 자료를 찾아보아도 정리 된 내용이 없어서 많은 어려움이 있었습니다. 이런 어려움을 덜고자 이 저장소를 개설하게 되었습니다.

> 본 저장소의 개설자는 Mac OS X 환경을 사용하고 있습니다. C,C++의 언어는 운영체제의 영향을 많이 받기 때문에 운영체제가 다르다면 이 튜토리얼을 따라해도 제대로 동작하지 않을 수 있습니다.


### MPICH
MPI(Message Passing Interface)는 병렬 프로그래밍을 위해서 고안된 표준(Standard)입니다. MPI는 아이디어가 먼저 제안이 되었고 구현이 나중에 이루어졌기 때문에 MPI 프로그래밍을 할 수 있는 여러 라이브러리가 존재합니다.

우리는 여러 구현된 라이브러리 중 **미시시피 주립 대학**과 **아르곤 국립 연구소**가 제작한 MPICH3라는 라이브러리를 사용합니다. MPI 표준을 관리하는 MPI 포럼에서는 2015년 MPI-3 버전의 표준을 제정하였습니다. 과거 프로그래머들 사이에서는 MPI와 같은 라이브러리를 사용하기 보다는 언어 차원에서 병렬 프로그래밍을 지원하도록 하자는 움직임이 유행이었습니다. 그에 따라 탄생한 것이 Haskell 등의 언어에서 발전된 함수형 프로그래밍 입니다. 병렬 컴퓨팅의 거의 모든 문제가 공유 메모리 상에서 여러 프로세스가 변수의 값을 바꾸는 것에서 발생한다고 생각해서 변수를 애초에 불변(immutable)하도록 만들자는 것이 기본 원칙이고 그에 따라 참조불변성, Map/Reduce등의 여러 개념들이 탄생했습니다.

그래서 그런지 최근 만들어지고 있는 언어에서는 한 번 변수를 선언하면 값을 바꿀 수 없도록 하는 문법을 제공하는 경우가 많습니다. 또한 고루틴 같이 언어 차원에서 병렬 프로그래밍 만을 위한 문법을 제공하고 있기 때문에 정말 특수한 환경에서 MPI를 사용하는 일은 드물 것 같습니다. 다만, MPI가 예전에 고민하고 여러 해를 거쳐서 논의가 이루어진 표준이기 때문에 그 개념은 익혀두어도 좋을 것 같스빈다.

### MPICH 설치
MPICH 라이브러리는 [mpich.org](https://www.mpich.org/downloads/) 사이트에서 다운로드 받을 수 잇습니다. 해당 라이브러리를 다운로드 받은 후 make를 이용해 설치합니다.

```sh
~$ tar -xvf mpich-3.3.1.tar.gz
~$ cd mpich-3.3.1
~$ ./configure --disable-fortran

Configuring MPICH version 3.3.1 with  '--disable-fortran'
Running on system: Darwin idohyeon-ui-MacBook-Pro.local 19.0.0 Darwin Kernel Version 19.0.0: Sat Aug 31 18:49:12 PDT 2019; root:xnu-6153.11.15~8/RELEASE_X86_64 x86_64

~$ sudo make install
```

위 가이드를 따라 제대로 설치 하셨다면 `mpiexec`명령어를 실행해서 실제 mpi가 설치 되었는지 확인할 수 있습니다.

```sh
~$ mpiexec --version

HYDRA build details:
    Version:                                 3.3.1
    Release Date:                            Wed Jun  5 14:57:33 CDT 2019
    CC:                              gcc    
    CXX:                             g++    
    F77:                             gfortran   
    F90:                             gfortran
```