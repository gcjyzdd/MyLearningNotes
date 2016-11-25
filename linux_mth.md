# Limux Multithreading

## basics

### get process id and thread id

```cpp
//get_pid.c
#include <stdio.h>
#include <pthread.h>
#include <stdlib.h>
#include <string.h>

int main()
{
	pid_t pid;
	pthread_t tid;
	
	pid=getpid();
	tid=pthread_self();
	
	printf("pid is %u, tid is 0x%x\n",pid,tid);
	
	return 0;

}

$gcc get_pid.c -pthread -o get_pid
$./get_pid
pid is 29903, tid is 0x3350f700
```
The function to get the parent process id is `getppid()`

### error number
explanation of error numbers are defined in
`/usr/include/asm-generic/errno.h`

### create threads

The function to create to thread is 
```cpp
int pthread_create(pthread_t *thread, 
					const pthread_attr_t *attr,
                    void *(*start_routine) (void *), 
					void *arg);

```
We can get the manuel of this function by type
`$man pthread_create`
in a terminal.

An example here:
```cpp
#include <stdio.h>
#include <pthread.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>

void print_id(char *s)
{
	pid_t pid;
	pthread_t tid;
	
	pid=getpid();
	tid=pthread_self();
	
	printf("%s pid is %u, tid is 0x%x",s,pid,tid);
}

void *thread_fun(void *args)
{
	print_id(args);
	return (void*)0;
}

int main()
{
	pthread_t ntid;
	int err;
	
	err=pthread_create(&ntid, NULL, thread_fun, "New thread");
	if (err!=0)
	{
		printf("create new thread failed\n");
		return 0;
	}

	print_id("main thread : ");
	sleep(2);
	
	return 0;
}

//output
New thread pid is 2715, tid is 0xb3426700
main thread :  pid is 2715, tid is 0xb3c09700

```
### main thread
use `pthread_exit` exit main() will not terminate all threads. It will wait all threads to join.

An example here
```cpp
#include <stdio.h>
#include <pthread.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>



struct student{
	int age;
	char name[20];
};

void *thread_fun(void *stu)
{
	printf("student age is %d, name is %s\n",((struct student*)stu)->age,((struct student*)stu)->name);
	return (void*)0;	
}

int main(int argc,char *argv[])
{
	pthread_t tid;
	int err;
	
	struct student stu;
	stu.age=20;
	// char cannot use '='
	memcpy(stu.name,"zhangsan",20);
	
	err=pthread_create(&tid,NULL,thread_fun,(void*)(&stu));
	if (err!=0)
	{
		printf("Create new pthread failed.\n");
		return 0;
	}
	
	int i;
	for(i=0;i<argc;i++)
	{
		printf("main thread args is %s\n", argv[i]);
	}
	
	// wait for new thread.
	sleep(1);
	
	return 0;
}
//output
$ ./main_thread sdf wfsd sdfdxac sefdasc ewrfcSD
main thread args is ./main_thread
main thread args is sdf
main thread args is wfsd
main thread args is sdfdxac
student age is 20, name is zhangsan
main thread args is sefdasc
main thread args is ewrfcSD

```
### thread status
1.就绪
2.运行
3.阻塞
4.终止

## L3
###terminate 
exit will terminate all threads.
1. return
2. cancel
3. pthread_exit

An example:
```cpp
#include <stdio.h>
#include <pthread.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>


void *thread_fun(void* arg)
{
	if (strcmp("1",(char*)arg)==0)
	{
		printf("new thread return!\n");
		return (void*)1;
	}
	
	if(strcmp("2",(char*)arg)==0)
	{
		printf("new thread pthread_exit!\n");
		pthread_exit((void*)2);
	}
	
	if(strcmp("2",(char*)arg)==0)
	{
		printf("new thread exit!\n");
		exit(3);
	}
}

int main(int argc,char* argv[])
{
	int err;
	pthread_t tid;
	
	err=pthread_create(&tid,NULL,thread_fun,(void*)argv[1]);
	
	if (err!=0)
	{
		printf("New thread failed!\n");
		return 0;
	}
	
	sleep(1);
	printf("main thread.\n");
	return 0;
}
```
### thread join线程连接
NAME
`pthread_join` - join with a terminated thread

SYNOPSIS
```cpp
#include <pthread.h>

       int pthread_join(pthread_t thread, void **retval);
```
Compile and link with `-pthread`.

DESCRIPTION

The  `pthread_join()`  function waits for the `thread` specified by thread to terminate.  If that thread has already terminated, then `pthread_join()` returns immediately.  The thread specified by thread must be joinable.

If `retval` is not NULL, then `pthread_join()` copies the exit status of the target thread (i.e.,  the  value  that  the  target thread  supplied  to  `pthread_exit(3)`)  into  the  location  pointed to by `*retval`.  If the target thread was canceled, then `PTHREAD_CANCELED` is placed in *retval.

If multiple threads simultaneously try to join with the same thread, the results  are  undefined. If  the  thread  calling `pthread_join()` is canceled, then the target thread will remain joinable (i.e., it will not be detached).

If a thread is detached, it cannot be joined and `pthread_join()` will return an error.

An example here.
```cpp
#include <stdio.h>
#include <pthread.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/types.h>

void *thread_fun1(void * arg)
{
	printf("I am thread 1\n");
	return (void*)1;
}

void *thread_fun2(void *arg)
{
	printf("I am thread 2\n");
	pthread_detach(pthread_self());
	pthread_exit((void*)2);

}

int main()
{
	int err1,err2;
	pthread_t tid1,tid2;
	void *rval1,*rval2;
	
	err1=pthread_create(&tid1,NULL,thread_fun1,NULL);
	err2=pthread_create(&tid2,NULL,thread_fun2,NULL);
	
	if (err1||err2)
	{
		printf("Create new thread failed.\n");
		return 0;
	}
	
	printf("I am main thread.\n");
	
	printf("Join1 rval is :%d\n" ,pthread_join(tid1,&rval1));
	printf("Join2 rval is :%d\n" ,pthread_join(tid2,&rval2));
	
	printf("thread1 exit code is %d\n",(int*)rval1);
	printf("thread2 exit code is %d\n",(int*)rval2);	
	
	printf("I am mian thread.\n");
	return 0;
}
// output
I am main thread.
I am thread 2
I am thread 1
Join1 rval is :0
Join2 rval is :22# means that the thread is not joinable.
thread1 exit code is 1
thread2 exit code is 1141306032
I am mian thread.

```



