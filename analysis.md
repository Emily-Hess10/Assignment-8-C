

I tested row-major and column-major traversal using four different matrix sizes.

| Matrix Size | Row-Major | Column-Major | Column/Row Ratio |
|---|---:|---:|---:|
| 1,000 × 1,000 | 3 ms | 5 ms | 1.67x |
| 2,000 × 2,000 | 13 ms | 35 ms | 2.69x |
| 4,000 × 4,000 | 44 ms | 170 ms | 3.86x |
| 8,000 × 8,000 | 179 ms | 760 ms | 4.25x |

Row-major traversal was faster for every matrix size. As the matrix got larger, the difference between the two methods increased. For the largest matrix, column-major took 760 ms, while row-major took 179 ms.

Both methods produced the same sum, so they were processing the same matrix values.



I tested the program using three compiler optimization levels: `-O0`, `-O2`, and `-O3`.

| Optimization | Matrix Size | Row-Major | Column-Major | Ratio |
|---|---:|---:|---:|---:|
| -O0 | 1,000 | 3 ms | 6 ms | 2.00x |
| -O0 | 2,000 | 14 ms | 33 ms | 2.36x |
| -O0 | 4,000 | 45 ms | 186 ms | 4.13x |
| -O0 | 8,000 | 187 ms | 835 ms | 4.47x |
| -O2 | 1,000 | 0 ms | 2 ms | N/A |
| -O2 | 2,000 | 3 ms | 13 ms | 4.33x |
| -O2 | 4,000 | 10 ms | 163 ms | 16.30x |
| -O2 | 8,000 | 48 ms | 760 ms | 15.83x |
| -O3 | 1,000 | 0 ms | 2 ms | N/A |
| -O3 | 2,000 | 2 ms | 12 ms | 6.00x |
| -O3 | 4,000 | 8 ms | 159 ms | 19.88x |
| -O3 | 8,000 | 41 ms | 781 ms | 19.05x |

The `-O2` and `-O3` options improved row-major performance, but they did not eliminate the difference between the two methods. Column-major was still much slower because compiler optimization cannot completely fix an inefficient memory access pattern. The 0 ms results are below the timer's displayed precision, not necessarily zero execution time. Timing results can also vary between runs.


![Row-major assembly](row_major_assembly.png)



![Column-major assembly](column_major_assembly.png)

Both functions generate similar assembly instructions, including `mov`, `add`, `cmp`, and `jl`. These instructions move values, add numbers, compare loop counters, and control the loops.

The main difference is how the indexes change. In row-major traversal, the inner loop changes the column index, so the program accesses neighboring elements in the same row. In column-major traversal, the inner loop changes the row index, causing the program to jump between separately allocated rows. This makes column-major traversal less efficient because it does not use the CPU cache as effectively.



CPU cache is a small, fast type of memory that stores data the processor is likely to need again soon. It helps the computer avoid having to retrieve every value from slower main memory. When a program accesses memory, the CPU usually brings in a small block of nearby memory called a cache line. If the program needs nearby values, they may already be in the cache. This is called cache locality.

Row-major traversal works well with cache locality because it accesses the values in the same row one after another. In this program, each row is allocated as its own section of memory, and the values within that row are stored next to each other. When the program reads one value, nearby values are likely to be available in the same cache line. This helps the program process the matrix faster.

Column-major traversal works differently. It reads one value from a row, then jumps to another row to get the next value in that column. Since the rows are allocated separately, these jumps can cause more cache misses. A cache miss happens when the processor needs data that is not currently available in the cache, so it has to retrieve it from a slower level of memory. This can make the program take longer.

My benchmark results showed this difference clearly. With the 8,000 × 8,000 matrix, row-major took 179 ms, while column-major took 760 ms in the baseline test. The larger the matrix became, the more noticeable the performance difference was. Compiler optimizations improved some of the execution times, but they did not remove the problem with column-major memory access.

Overall, this lab showed me that the order in which a program accesses data can make a big difference in performance. Writing code that uses memory efficiently can be just as important as reducing the number of instructions in a program.



The ideas from this lab can help with my final project because data analytics programs often work with large amounts of information stored in arrays or tables. Accessing data in a consistent order can help a program run faster and avoid unnecessary memory delays. I can use what I learned about cache locality when organizing and processing data in future projects. This is especially useful when working with larger datasets.