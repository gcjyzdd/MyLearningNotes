# Handy functions for C/C++

## Time manipulations

### get execution
If you have `cygwin` installed, from it's bash shell, run your executable, say `MyProgram`, using the time utility, like so:
```bash
/usr/bin/time ./MyProgram
```
This will report how long the execution of your program took -- the output would look something like the following:
```bash
real    0m0.792s
user    0m0.046s
sys     0m0.218s
```

You could also manually modify your `C program` to instrument it using the `clock()` library function, like so:
```cpp
#include <time.h>
int main(void) {
    clock_t tStart = clock();
    /* Do your stuff here */
    printf("Time taken: %.2fs\n", (double)(clock() - tStart)/CLOCKS_PER_SEC);
    return 0;
}
```
