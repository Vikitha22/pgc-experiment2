<div align="center">

# Experiment 2

## Multithreaded Programming Using Pthreads and OpenMP

**Thread Creation • Work Distribution • Race Conditions • Synchronization • Performance Analysis**

![Language](https://img.shields.io/badge/Language-C-blue)
![Compiler](https://img.shields.io/badge/Compiler-GCC-orange)
![Pthreads](https://img.shields.io/badge/Library-Pthreads-green)
![OpenMP](https://img.shields.io/badge/Library-OpenMP-red)
![Platform](https://img.shields.io/badge/Platform-WSL%20Ubuntu-purple)
![Status](https://img.shields.io/badge/Status-Completed-success)

</div>

---

## Table of Contents

1. [Aim](#1-aim)
2. [Basic Concept](#2-basic-concept)
3. [Experimental Environment](#3-experimental-environment)
4. [Project Structure](#4-project-structure)
5. [Overall Experiment Flow](#5-overall-experiment-flow)
6. [Part A: Pthreads](#6-part-a-pthreads)
7. [Part B: OpenMP](#7-part-b-openmp)
8. [Part C: Performance Analysis](#8-part-c-performance-analysis)
9. [Pthreads vs OpenMP](#9-pthreads-vs-openmp)
10. [Correctness Verification](#10-correctness-verification)
11. [Compilation and Execution](#11-compilation-and-execution)
12. [Key Observations](#12-key-observations)
13. [Conclusion](#13-conclusion)

---

## 1. Aim

To develop multithreaded programs using **Pthreads and OpenMP** and understand:

- Thread creation and management
- Work distribution
- Race conditions
- Synchronization
- Thread coordination
- Performance improvement using multiple threads

---

## 2. Basic Concept

A **thread** is an execution path inside a program.

**Sequential program:** one thread performs all the work.

```text
               PROGRAM
                  |
                  v
                TASK
                  |
          -----------------
          |       |       |
        Task 1  Task 2  Task 3
                  |
                  v
               RESULT
```

**Multithreaded program:** the work is performed by multiple threads.

```text
                  PROGRAM
                     |
          ---------------------------
          |      |      |           |
          v      v      v           v
      Thread1 Thread2 Thread3   Thread4
          |      |      |           |
          v      v      v           v
        Work    Work   Work        Work
           \      |      |         /
            \     |      |        /
             --------------------
                     |
                     v
                  RESULT
```

This experiment uses two approaches:

| Approach | Characteristics |
|----------|-----------------|
| **Pthreads** | Explicit thread creation and management, programmer-controlled work distribution, mutex-based synchronization |
| **OpenMP** | Higher-level parallel programming model, compiler directives, critical sections, barriers, and reductions |

---

## 3. Experimental Environment

| Component | Details |
|-----------|---------|
| Host Operating System | Windows |
| Linux Environment | WSL Ubuntu |
| Programming Language | C |
| Compiler | GCC |
| Thread Library | POSIX Pthreads |
| Parallel Framework | OpenMP |
| Editor | Nano |
| Working Directory | `~/parallel_lab` |

---

## 4. Project Structure

```text
parallel_lab/
│
├── thread1.c          # Single thread creation
├── thread2.c          # Multiple threads
├── thread_sum.c       # Work distribution
├── race.c             # Pthreads race condition
├── mutex.c            # Mutex synchronization
├── pthread_perf.c     # Pthreads performance
│
├── omp1.c             # OpenMP parallel region
├── omp_sum.c          # OpenMP work sharing and reduction
├── omp_race.c         # OpenMP race condition
├── omp_critical.c     # OpenMP critical section
├── omp_barrier.c      # OpenMP barrier
├── omp_perf.c         # OpenMP performance
│
├── sequential.c       # Sequential baseline
│
├── images/
│   ├── execution_time.png
│   ├── speedup.png
│   └── efficiency.png
│
└── README.md
```

---

## 5. Overall Experiment Flow

```text
                         START
                           |
                           v
                 Understand Threads
                           |
                           v
                  Create One Thread
                           |
                           v
                Create Multiple Threads
                           |
                           v
                    Divide the Work
                           |
                           v
                      Shared Data
                           |
                           v
                    Race Condition
                           |
                           v
                    Synchronization
                           |
              -------------------------
              |                       |
              v                       v
          PTHREADS                 OPENMP
              |                       |
              v                       v
       pthread_create()        Parallel Region
              |                       |
              v                       v
        pthread_join()          Work Sharing
              |                       |
              v                       v
            Mutex                  Critical
              |                       |
              v                       v
        Correct Result             Barrier
              |                       |
              -----------+-------------
                         |
                         v
                Sequential Performance
                         |
                         v
                  Pthreads Performance
                         |
                         v
                   OpenMP Performance
                         |
                         v
                 Execution Time Graph
                         |
                         v
                       Speedup
                         |
                         v
                     Efficiency
                         |
                         v
                    Final Analysis
                         |
                         v
                        END
```

---

## 6. Part A: Pthreads

**Pthreads** stands for POSIX Threads. It provides explicit control over thread creation, execution, joining, and synchronization.

Important functions used:

- `pthread_create()`
- `pthread_join()`
- `pthread_mutex_lock()`
- `pthread_mutex_unlock()`

### Pthreads Flow

```text
             Main Program
                  |
                  v
           Create Thread(s)
                  |
                  v
             Assign Work
                  |
                  v
           Thread Executes
                  |
                  v
             Shared Data?
              /       \
            Yes        No
             |          |
             v          v
        Synchronize  Continue
             |
             v
        pthread_join()
             |
             v
           Result
```

### 6.1 Single Thread Creation: `thread1.c`

**Objective:** create a single thread and understand how the main thread waits for it.

**Observed output**

```text
Hello from the thread!
Main thread finished.
```

This demonstrates basic thread creation and completion.

### 6.2 Multiple Threads: `thread2.c`

**Objective:** create and execute multiple threads. Four threads were created.

**Observed execution order**

```text
Hello from Thread 1
Hello from Thread 2
Hello from Thread 4
Hello from Thread 3
All threads have finished.
```

The order of execution may vary because thread scheduling is controlled by the operating system.

### 6.3 Work Distribution: `thread_sum.c`

The work is divided among four threads.

| Thread | Partial Sum |
|--------|:-----------:|
| Thread 1 | 30 |
| Thread 2 | 70 |
| Thread 3 | 110 |
| Thread 4 | 150 |
| **Total** | **360** |

```text
                Total Work
                    |
          ---------------------
          |      |      |     |
          v      v      v     v
       Thread1 Thread2 Thread3 Thread4
          |      |      |     |
         30     70    110    150
           \      |      |    /
            \     |      |   /
             ----------------
                    |
                    v
                   360
```

The experiment demonstrates how a large task can be divided among multiple threads.

### 6.4 Race Condition: `race.c`

A **race condition** occurs when multiple threads access or modify shared data at the same time without proper synchronization.

**Observed result**

```text
Expected result : 400000
Actual result   : 167739
```

A second run gave a different value (`131342`), so the incorrect result changes from run to run.

The actual result is different because multiple threads update the shared variable concurrently.

```text
             Shared Variable
                    |
          ---------------------
          |         |         |
          v         v         v
       Thread 1  Thread 2  Thread 3
          |         |         |
          -------- Access -----
                    |
                    v
              Data Conflict
                    |
                    v
               Wrong Result
```

### 6.5 Mutex Synchronization: `mutex.c`

A **mutex** provides mutual exclusion and ensures that only one thread enters the protected critical section at a time.

**Observed result**

```text
Expected result : 400000
Actual result   : 400000
```

```text
               Thread
                 |
                 v
           Request Mutex
                 |
                 v
          Lock Available?
             /       \
           Yes        No
            |          |
            v          v
          Lock        Wait
            |          |
            v          |
        Critical       |
         Section       |
            |          |
            v          |
          Unlock <-----+
            |
            v
         Continue
```

The mutex prevents simultaneous modification of the shared variable.

---

## 7. Part B: OpenMP

OpenMP provides a higher-level approach to shared-memory parallel programming using compiler directives.

Main concepts demonstrated:

- Parallel regions
- Work sharing
- Reduction
- Critical sections
- Barriers

### 7.1 Parallel Execution: `omp1.c`

The program creates a parallel region and identifies the executing threads.

**Observed result:** 32 threads were used on the test system (`Hello from Thread X of 32`).

The exact order in which threads print their messages can vary because thread scheduling is not fixed.

```text
              Main Program
                   |
                   v
          #pragma omp parallel
                   |
       -------------------------
       |      |      |        |
       v      v      v        v
      T1     T2     T3       T4
       |      |      |        |
       ------- Parallel -------
                   |
                   v
               Complete
```

### 7.2 Work Sharing: `omp_sum.c`

The summation workload is distributed among OpenMP threads.

**Result:** `Total sum = 360`

This demonstrates parallel work distribution and combination of partial results.

### 7.3 Race Condition: `omp_race.c`

The shared variable is updated by multiple threads without synchronization.

**Observed result**

```text
Expected result : 400000
Actual result   : 100000
```

The incorrect result demonstrates a race condition. The exact wrong value depends on execution timing.

```text
             Shared Variable
                    |
          ---------------------
          |         |         |
          v         v         v
        OpenMP    OpenMP    OpenMP
        Thread    Thread    Thread
          |         |         |
          -------- Update -----
                    |
                    v
             Race Condition
                    |
                    v
               Wrong Result
```

### 7.4 Critical Section: `omp_critical.c`

The `critical` directive protects the shared section of code.

**Observed result**

```text
Expected result : 400000
Actual result   : 400000
```

```text
              OpenMP Threads
                    |
                    v
             Critical Section
                    |
          ---------------------
          |                   |
     One thread          Other threads
       enters                wait
          |
          v
      Update Data
          |
          v
        Leave
          |
          v
      Next Thread
```

The critical section prevents multiple threads from executing the protected code simultaneously.

### 7.5 Barrier: `omp_barrier.c`

A **barrier** is a synchronization point where threads wait until all required threads reach the same point.

**Observed behaviour:** all Stage 1 messages appeared before any Stage 2 message.

```text
 Thread 1 ---- Stage 1 ----\
 Thread 2 ---- Stage 1 -----\
 Thread 3 ---- Stage 1 ------> BARRIER ----> Stage 2
 Thread 4 ---- Stage 1 -----/
```

This demonstrates thread coordination.

---

## 8. Part C: Performance Analysis

The final part evaluates whether using multiple threads reduces execution time.

The same computational workload was executed using sequential execution, Pthreads, and OpenMP.

**Workload:** `N = 1,000,000,000`

**Expected result:** `499999999500.00`. All implementations produced the correct result.

### 8.1 Sequential Baseline

The sequential program was run five times and averaged.

| Run | Time (s) |
|:---:|:--------:|
| 1 | 1.353895 |
| 2 | 1.355794 |
| 3 | 1.349621 |
| 4 | 1.353422 |
| 5 | 1.353365 |
| **Average** | **1.353219** |

This average is the **sequential baseline** used for speedup and efficiency.

```text
             Sequential Program
                    |
                    v
              Measure Time
                    |
                    v
              Baseline Time
                    |
          ---------------------
          |                   |
          v                   v
      Compare              Compare
      Pthreads              OpenMP
```

### 8.2 Pthreads Performance

| Threads | Execution Time |
|:-------:|:--------------:|
| 1 | 1.348142 s |
| 2 | 0.680737 s |
| 4 | 0.358872 s |
| 6 | 0.241345 s |
| 16 | 0.144812 s |

### 8.3 OpenMP Performance

| Threads | Execution Time |
|:-------:|:--------------:|
| 1 | 1.409294 s |
| 2 | 0.715560 s |
| 4 | 0.360803 s |
| 6 | 0.241608 s |
| 16 | 0.140692 s |

### 8.4 Performance Comparison

| Threads | Pthreads (s) | OpenMP (s) |
|:-------:|:------------:|:----------:|
| 1 | 1.348142 | 1.409294 |
| 2 | 0.680737 | 0.715560 |
| 4 | 0.358872 | 0.360803 |
| 6 | 0.241345 | 0.241608 |
| 16 | 0.144812 | 0.140692 |

### 8.5 Performance Graphs

**Figure 1: Execution Time vs Number of Threads**

![Execution Time vs Number of Threads](images/execution_time.png)

**Figure 2: Speedup vs Number of Threads**

![Speedup vs Number of Threads](images/speedup.png)

**Figure 3: Efficiency vs Number of Threads**

![Efficiency vs Number of Threads](images/efficiency.png)

### 8.6 Speedup Analysis

```text
Speedup = Sequential Time / Parallel Time
```

Example (OpenMP, 16 threads): 1.353219 / 0.140692 = **9.62**

| Threads | Pthreads | OpenMP |
|:-------:|:--------:|:------:|
| 1 | 1.004x | 0.960x |
| 2 | 1.988x | 1.891x |
| 4 | 3.771x | 3.751x |
| 6 | 5.608x | 5.601x |
| 16 | 9.345x | 9.618x |

**Observation:** speedup increases with the number of threads. At 16 threads, Pthreads reached about 9.35x and OpenMP about 9.62x.

### 8.7 Efficiency Analysis

```text
Efficiency = Speedup / Number of Threads x 100
```

Example (OpenMP, 16 threads): 9.62 / 16 x 100 = **about 60.1%**

| Threads | Pthreads | OpenMP |
|:-------:|:--------:|:------:|
| 1 | 100.38% | 96.02% |
| 2 | 99.39% | 94.56% |
| 4 | 94.27% | 93.76% |
| 6 | 93.45% | 93.35% |
| 16 | 58.40% | 60.11% |

**Observation:** efficiency stays high (above 93%) up to 6 threads and drops to about 58 to 60% at 16 threads. Adding threads does not automatically give proportional speedup. The 100.38% value for 1-thread Pthreads is within normal run-to-run timing variation.

### 8.8 Why Speedup Is Not Perfectly Linear

Ideal 16-thread time would be about 1.35 / 16 = **0.084 s**, but the measured OpenMP time was **0.141 s**. Increasing the number of threads does not guarantee proportional speedup. Factors affecting performance include:

- Thread creation overhead
- Thread scheduling
- Synchronization overhead
- Memory access
- Operating-system activity
- Shared-resource contention
- Work distribution
- Available CPU resources

```text
        More Threads
             |
             v
     More Parallel Work
             |
             v
    Lower Execution Time
             |
             v
  But Increasing Overhead
             |
             v
 Less Than Perfect Scaling
```

---

## 9. Pthreads vs OpenMP

| Feature | Pthreads | OpenMP |
|---------|----------|--------|
| Thread creation | `pthread_create()` | `#pragma omp parallel` |
| Thread waiting | `pthread_join()` | Runtime synchronization |
| Work distribution | Programmer controlled | OpenMP constructs |
| Shared-data protection | Mutex | `critical` |
| Coordination | Join / synchronization | Barrier |
| Programming level | Lower-level | Higher-level |
| Ease of use | More manual | Simpler |
| Thread management | Explicit | Mostly runtime managed |

Both approaches support shared-memory parallel programming, but they provide different levels of programmer control.

---

## 10. Correctness Verification

| Experiment | Expected | Actual | Result |
|------------|:--------:|:------:|--------|
| Pthreads Sum | 360 | 360 | Correct |
| Pthreads Race | 400000 | 167739 | Race demonstrated |
| Pthreads Mutex | 400000 | 400000 | Correct |
| OpenMP Sum | 360 | 360 | Correct |
| OpenMP Race | 400000 | 100000 | Race demonstrated |
| OpenMP Critical | 400000 | 400000 | Correct |
| Performance Programs | 499999999500.00 | 499999999500.00 | Verified |

---

## 11. Compilation and Execution

### Pthreads

```bash
# Compile
gcc thread1.c -o thread1 -pthread
gcc thread2.c -o thread2 -pthread
gcc thread_sum.c -o thread_sum -pthread
gcc race.c -o race -pthread
gcc mutex.c -o mutex -pthread

# Run
./thread1
./thread2
./thread_sum
./race
./mutex
```

### OpenMP

```bash
# Compile
gcc omp1.c -o omp1 -fopenmp
gcc omp_sum.c -o omp_sum -fopenmp
gcc omp_race.c -o omp_race -fopenmp
gcc omp_critical.c -o omp_critical -fopenmp
gcc omp_barrier.c -o omp_barrier -fopenmp

# Run
./omp1
./omp_sum
./omp_race
./omp_critical
./omp_barrier
```

### Performance Programs

```bash
# Compile
gcc sequential.c -o sequential
gcc pthread_perf.c -o pthread_perf -pthread
gcc omp_perf.c -o omp_perf -fopenmp

# Run
./sequential
./pthread_perf      # enter 1, 2, 4, 6, 16 when prompted
./omp_perf          # enter 1, 2, 4, 6, 16 when prompted
```

---

## 12. Key Observations

**Part A: Pthreads**

1. `pthread_create()` creates one additional thread per call. `pthread_join()` makes the main thread wait for it.
2. Thread execution order is not guaranteed. The operating system schedules the threads.
3. Dividing the array among four threads gave partial sums 30, 70, 110, 150, which combined to the correct total of 360.
4. Without synchronization, the shared counter ended at 167739 instead of 400000 and changed on every run. This confirms a race condition.
5. Protecting `counter++` with a mutex gave the correct 400000.

**Part B: OpenMP**

6. A single `#pragma omp parallel` created a team of 32 threads on the test system, without any explicit thread creation.
7. `parallel for` with `reduction(+:total_sum)` divided the loop and combined partial results safely (total 360).
8. OpenMP does not make shared data safe automatically: `omp_race.c` gave 100000 instead of 400000.
9. `#pragma omp critical` fixed the race and gave 400000.
10. `#pragma omp barrier` guaranteed that every thread finished Stage 1 before any thread started Stage 2.

**Part C: Performance**

11. Both Pthreads and OpenMP reduced execution time as threads increased, from about 1.35 s (1 thread) to about 0.14 s (16 threads).
12. At 16 threads, OpenMP (9.62x) was marginally ahead of Pthreads (9.35x). The two were almost identical at 4 and 6 threads.
13. Efficiency was above 93% up to 6 threads and dropped to about 58 to 60% at 16 threads, so speedup is sub-linear because of parallel overhead.
14. All performance programs produced the same result, `499999999500.00`, confirming correctness.

---

## 13. Conclusion

This experiment demonstrated the fundamental concepts of multithreaded programming using Pthreads and OpenMP.

The experiments covered:

- Thread creation
- Multiple-thread execution
- Work distribution
- Race conditions
- Mutex synchronization
- OpenMP parallel regions
- OpenMP work sharing
- Critical-section synchronization
- Barrier coordination
- Execution-time measurement
- Speedup calculation
- Efficiency analysis
- Pthreads and OpenMP comparison

The synchronization experiments demonstrated the importance of protecting shared data when multiple threads access the same resource.

The performance experiments showed that multiple threads can substantially reduce execution time for a large computational workload. However, speedup is not perfectly linear because of thread management, scheduling, synchronization, and other system overheads.

Overall, the experiment provides practical understanding of thread management, parallel execution, synchronization, coordination, and performance analysis.

<div align="center">

**Experiment 2: Multithreaded Programming Using Pthreads and OpenMP**

Implemented • Tested • Verified • Analyzed

</div>
