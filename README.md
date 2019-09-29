## MPI 프로그래밍 튜토리얼 및 정리 레포지토리
본 저장소는 분산병렬컴퓨팅 강의 중 MPI 프로그래밍 내용을 정리하기 위해 개설된 공간입니다. MPI 라이브러리의 기본적인 설치 방법 및 사용방법을 구글 통해서 찾아보려고 했으나 영문 및 한글로 자료를 찾아보아도 정리 된 내용이 없어서 많은 어려움이 있었습니다. 이런 어려움을 덜고자 이 저장소를 개설하게 되었습니다.

> 본 저장소의 개설자는 Mac OS X 환경을 사용하고 있습니다. C,C++의 언어는 운영체제의 영향을 많이 받기 때문에 운영체제가 다르다면 이 튜토리얼을 따라해도 제대로 동작하지 않을 수 있습니다.


### 1. MPICH 소개 및 주저리
MPI(Message Passing Interface)는 병렬 프로그래밍을 위해서 고안된 표준(Standard)입니다. MPI는 아이디어가 먼저 제안이 되었고 구현이 나중에 이루어졌기 때문에 MPI 프로그래밍을 할 수 있는 여러 라이브러리가 존재합니다.

우리는 여러 구현된 라이브러리 중 **미시시피 주립 대학**과 **아르곤 국립 연구소**가 제작한 MPICH3라는 라이브러리를 사용합니다. MPI 표준을 관리하는 MPI 포럼에서는 2015년 MPI-3 버전의 표준을 제정하였습니다. 이 표준을 이용해 구현된 라이브러리가 앞으로 사용할 MPICH입니다. MPICH는 인텔, 마이크로소프트, IBM등 여러 회사에서 널리 이용되고 있습니다.

### 2. MPICH 설치
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

### 3. MPI Hello, World!
프로그래밍을 처음 배울 때 가장 먼저 하는 것이 `Hello, World!`를 출력하는 일입니다. 이번에는 MPI 프로그래밍을 배우기 전에 간단한 프로그램 하나를 작성하고 컴파일 하는 방법에 대해 다룹니다.

```c
// main.c
#include <mpi.h>
#include <stdio.h>

int main(int argc, char** argv) {
    // Initialize the MPI environment
    MPI_Init(NULL, NULL);

    // Get the number of processes
    int world_size;
    MPI_Comm_size(MPI_COMM_WORLD, &world_size);

    // Get the rank of the process
    int world_rank;
    MPI_Comm_rank(MPI_COMM_WORLD, &world_rank);

    // Get the name of the processor
    char processor_name[MPI_MAX_PROCESSOR_NAME];
    int name_len;
    MPI_Get_processor_name(processor_name, &name_len);

    // Print off a hello world message
    printf("Hello world from processor %s, rank %d out of %d processors\n",
           processor_name, world_rank, world_size);

    // Finalize the MPI environment.
    MPI_Finalize();
}
```

위 예제는 [이 링크](https://github.com/wesleykendall/mpitutorial/blob/gh-pages/tutorials/mpi-hello-world/code/mpi_hello_world.c) 에서 가져 온 예제입니다. 이 예제는 로컬 컴퓨터의 `rank`, `그룹 크기`, `프로세서 이름`를 출력합니다.

#### 컴파일
제가 사용하고 있는 Mac 운영체제에서는 C로 작성된 프로그램을 컴파일 하기 위해서 `gcc`, `clang`같은 명령어를 사용할 수 있는데요 MPI를 사용하는 코드에서는 위 컴파일러를 사용하면 에러가 발생합니다. 우리는 위 명령어 대신 `mpicc`명령어를 사용해서 컴파일 해야 합니다.

```sh
$ mpicc -o main main.c
$ ./main

Hello world from processor <processor-name>, rank 0 out of 1 processors
```

컴파일 하고 프로그램을 실행하면 프로세서 이름과 rank, 그룹 크기가 출력 되는데요. MPI는 프로세서들을 논리적으로 그룹화 하고 있습니다. 출력 결과를 해석하면 그 그룹안에 포함된 프로세서의 개수는 한 개 이고 해당 프로세서의 rank는 0번이라는 의미입니다.

`rank`란 프로세서를 식별하는 식별자입니다. 프로세서가 16개가 연결되어 있다면 각 프로세서는 `0~15번`의 rank를 가지게 됩니다. 이 중 0번 rank가 부모(Master) 프로세서의 역할을 가지며 자식(Slave) 프로세서에게 메시지를 전달합니다.