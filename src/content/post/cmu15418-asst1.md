---
title: cmu15418-asst1
description: 'asst1'
publishDate: 2024-12-30
---

# asst1
## prog1_mandelbrot_threads 


本实验要求我们用thread实现并行计算“分型图形”。

按照要求首先保存每个线程的参数，然后启动线程执行并行计算。
```cpp
for (int i=0; i<numThreads; i++) {
// TODO: Set thread arguments here.
args[i].x0 = x0;
args[i].y0 = y0;
args[i].x1 = x1;
args[i].y1 = y1;
args[i].width = width;
args[i].height = height;
args[i].maxIterations = maxIterations;
args[i].numThreads = numThreads;
args[i].output = output;
args[i].threadId = i;
}
```

选择不同的线程数量观察加速比。

cs149的视频中讲解了两种并行的方式：
1. 第一种是先用总行数除以线程数量，得到每个线程计算的行数，然后分别计算.  
```cpp
int row = args->height / args->numThreads;
int startRow = args->threadId * row;
int endRow = startRow + row;
if (args->threadId == args->numThreads - 1) {
endRow = args->height;
}
mandelbrotSerial(args->x0, args->y0, args->x1, args->y1, args->width, args->height, startRow, endRow, args->maxIterations, args->output);
```
2. 第二种是固定每个线程计算n行，然后分别执行
```cpp
int numRowsPerThread = 4;
int startRow = numRowsPerThread * args->threadId;
for (size_t i = startRow; i < args->height; i += numRowsPerThread * args->numThreads)
mandelbrotSerial(args->x0, args->y0, args->x1, args->y1, args->width, args->height, i, i + numRowsPerThread, args->maxIterations, args->output);
```

视频中讲解了第二种的计算方式优于第一种，因为分型图形中有一些计算量比较大，耗时较多，集中到一个线程处理会拖累整个进程的执行时间，因此第二种计算速度较快。

实验结果：执行时间/加速比

| 线程数量 | 1           | 2            | 4            | 8           | 16          |
| ---- | ----------- | ------------ | ------------ | ----------- | ----------- |
| 第一种  | 233.379/1.0 | 122.249/1.90 | 100.082/2.33 | 64.450/3.56 | 43.683/5.25 |
| 第二种  | 233.189/1.0 | 121.901/1.91 | 63.059/3.70  | 40.282/5.48 | 40.246/5.90 |


可见在线程数量较少时差距并不明显，随着线程的数量逐渐增多，第二种方式的加速比更大，线程数由8增加到16时，加速比不变，因为电脑只有8个核。

