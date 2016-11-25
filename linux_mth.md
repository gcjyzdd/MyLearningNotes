# Limux Multithreading

## basics

### get process id and thread id

```C
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
	
	printf("pid is %u, tid is %x\n",pid,tid);
	
	return 0;

}

$gcc get_pid.c -pthread -o get_pid
$./get_pid
pid is 29903, tid is 3350f700
```

