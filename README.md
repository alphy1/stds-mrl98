# Statistical Techniques for Data Science and Robotics
## Assignment 1. Report
### Alfiya Mussabekova, DS01

To implement MRL98 algorithm, I used paper[1] that presents three different policies for `collapse` operation. My implementation uses the new policy presented in the paper. 

MRL98 has five main functions:
1. `_new` takes as arguments index of the empty buffer, `[start, end)` range from the dataset to put in the empty buffer, and the level of the new buffer. It populates empty buffer with the values from `dataset[start:end]`, assigns it weight **1** and given level. If the number of elements is less than `k` then it adds `+inf` and `-inf` values until buffer is full.

2. `_collapse` takes as arguments index of the buffer from which collapse will start, and the level of the new collapsed buffer. The function collapses input buffers and puts the result into `start` buffer. 
    For my implementation, buffers with lower indexes will always have level greater or equal than buffers with higher indexes. For example, level list can look like this `[3 2 2 1 0 0 0]`. In this case, collapse will happen to the last three buffers and updated level list will look like this `[3 2 2 1 1 0 0]`. Therefore, we need only the index from where to start collapsing since by policy of the new algorithm only buffers with the smallest level can be collapsed.

3. `_output` is called only once when data sequence is finished. The function sorts elements inside all buffers and computes sum of their weights.

4. `mrl98` - the algorithm itself that calls `_new` and `_collapse` while reading the dataset depending on the collapse policy and calls `_output` when dataset is finished.

5. `find_quantiles` takes as an argument list of quantiles to find, sorts it, and computes each quantile value. Just like in collapse operation, it goes through all buffers in parallel using `cursors` to find value that should be on the quantile positions. It does not use any additional memory for merging buffers. 

### Testing
To test I used values of **b** and **k** provided in the paper. The table below represents these values.
![](https://github.com/alphy1/stds-mrl98/blob/main/mrl_bk.png)

1. depending on the dataset size and error value, take `b` and `k` values from the created dictionary
2. populate random sequence
3. run the MRL98 algorithm and measure execution time
4. calculate memory usage 
5. compute 20 different quantiles using mrl algorithm
6. find real quantiles 
7. plot the difference between them

The graphs show that with smaller error the difference between quantiles computed by MRL98 and real ones are smaller and less often. For example, with error = **0.1** the difference is up to **20** and just a few values match while with error = **0.001** the difference is maximum **1** and most of the values match exactly.

![Difference when error = 0.1](https://github.com/alphy1/stds-mrl98/blob/main/mrl_error01.png "Difference when error = 0.1")
![Difference when error = 0.001](https://github.com/alphy1/stds-mrl98/blob/main/mrl_error001.png)

Figure 1 and Figure 2 show how time depends on N and error.

<figure>
  <figcaption>Figure 1. Time depending on N</figcaption>
  <img src="https://github.com/alphy1/stds-mrl98/blob/main/time_N.png" alt=".." title="Optional title" />
</figure>

<figure>
  <figcaption>Figure 2. Time depending on error</figcaption>
  <img src="https://github.com/alphy1/stds-mrl98/blob/main/time_sec.png" alt=".." title="Optional title" />
</figure>


Figure 3 and Figure 4 show how memory depends on N and error.

<figure>
  <figcaption>Figure 3. Memory depending on N</figcaption>
  <img src="https://github.com/alphy1/stds-mrl98/blob/main/mem_N.png" alt=".." title="Optional title" />
</figure>

<figure>
  <figcaption>Figure 4. Memory depending on error</figcaption>
  <img src="https://github.com/alphy1/stds-mrl98/blob/main/mem_err.png" alt=".." title="Optional title" />
</figure>

### Conclusion
The graphs show how time and memory depend on the **N** - length of the dataset and **e** - error. Time consumption depends only on the length of the dataset, Figure 2 shows that graphs are linear for time depending on error. Memory usage depends on both the length and the error as the values of **b** and **k** are different for different **(N, e)** pairs. 

To calculate memory usage, I calculated size of all lists that I used during my implementation (not only buffers), so it is bigger than the ones that presented in the paper. I used:
1. **bk** for buffers
2. **b** for levels list
3. **b** for weights
4. **b** for cursors (needed during collapse operation and to not create every time new list, just allocating memory in advance)
5. **k** for collapsed buffer (same as cursors, it needed every collapse operation)

In total, **bk+b+b+b+k = bk + 3b + k** memory is needed.



References:
1. http://www.cs.umd.edu/~samir/498/manku.pdf
2. https://github.com/alphy1/stds-mrl98
