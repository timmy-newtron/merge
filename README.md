# Interval merging in Go

## Problem

Implement a function `merge` that, given a list of intervals, merges all overlapping intervals and returns the result as a list of disjoint intervals.

## Solution

The solution is implemented in the `merge` function in the `merge.go` file. The function takes a list of intervals and returns a list of disjoint intervals.
To describe an interval, I use a struct with two fields: `Start` and `End`. The `Start` field represents the start of the interval and the `End` field represents the end of the interval. Both fields are of type `int`. Along with the Interval struct, I also define a constructor function `New` that takes two integers and returns an Interval struct. For simplicity of testing whether two intervals overlap, I added a method `Overlaps` to the Interval struct. The method takes another Interval struct as an argument and returns a boolean value indicating whether the two intervals overlap.

One could think of extending the Interval struct to be generic, but I decided to keep it simple for the sake of this exercise.

### Algorithm

The merging algorithm itself works in three steps. First, we check if the given list of intervals is empty and if so, we return an empty list. Second, the intervals are sorted by their start value. Third, the sorted intervals are merged if they overlap. The second step ensures that if two adjacent intervals do not overlap, the remaining intervals do not overlap either. We use this invariant to avoid checking all intervals for overlaps. Instead, we only check the last interval in the list for overlaps with the current interval. If the two intervals overlap, we merge them. Otherwise, we append the current interval to the list of merged intervals. First sorting the intervals and then merging them allows us to merge the intervals in O(n log n) time.

In pseudocode, the algorithm looks as follows:

```pseudo
function Merge(intervals):
    if intervals is empty:
        return empty list
    sort intervals by start value
    merged = [intervals[0]]
    for interval in intervals:
        if interval overlaps with merged[-1]:
            merged[-1] = MergeTwo(merged[-1], interval)
        else:
            merged.append(interval)
    return merged
```

where `MergeTwo` is a function that takes two intervals and returns a merged interval:

```pseudo
function MergeTwo(interval1, interval2):
    return interval with start value of min(interval1.start, interval2.start) and end value of max(interval1.end, interval2.end)
```



### Time spent on the solution

Realistically, I spent about 5-6 hours on the solution. I spent about 2 hours on the algorithm itself (train of thought) and 2 hours on the implementation. The latter includes the time I spent on setting up and getting familiar with Go, writing the code and writing tests. The remaining time was spent on writing this README file, adding test cases and writing comments.

## Train of thought

In case you are interested in how I came up with my solution, you can find my notes in the `train_of_thought.pdf` file.

## Runtime and memory complexity

The runtime complexity of the solution is O(n log n) and the memory complexity is O(n).

The runtime complexity is O(n log n) because the merging algorithm is based on sorting the intervals by their start value. Sorting the intervals takes O(n log n) time, as accodring to [[1](https://stackoverflow.com/a/71957898)], Go's sort.Slice uses pdqsort [[2](https://github.com/orlp/pdqsort)] with an average and worst case runtime of O(n log n) . After sorting the intervals, the algorithm iterates over the sorted intervals and merges them. The iteration takes O(n) time. Therefore, the overall runtime complexity is O(n log n + n) = O(n log n).

The memory complexity is O(n) because the algorithm uses a slice of intervals to store the merged intervals. The slice can contain at most n intervals, where n is the number of intervals in the input. In the worst case scenario, the input contains n intervals that do not overlap. We thus duplicate the input and store it in the slice. Therefore, the memory complexity is O(2n) = O(n). The memory usage of pdqsort is O(log n) and thus can be neglected in this case.

## Robustness

I interpreted the term `robustness` as the ability of a system to behave in a predictable manner despite unforeseeable circumstances. In this case, the system is the interval merging algorithm and the unforeseeable circumstances are the different cases that the algorithm has to handle.

The solution is robust to the following cases:

- Empty intervals
- Intervals with negative values
- Intervals with zero values
- Intervals with negative and zero values
- Intervals with positive values
- Intervals with positive and zero values
- Intervals with positive and negative values
- Intervals with positive, negative and zero values
- Intervals with positive, negative and zero values in different orders
- Intervals with overlapping values
- Intervals with overlapping values in different orders
- Intervals with overlapping values and negative values
- Intervals with overlapping values and zero values
- Intervals with overlapping values and positive values
- Intervals with overlapping values, negative values and zero values

### How can robustness be guaranteed for large inputs?

The solution is robust to large inputs because it does not use recursion. The algorithm iterates over the sorted intervals and merges them. Therefore, the algorithm does not use the call stack and thus does not run into stack overflow errors. The algorithm also does not use recursion to sort the intervals. As mentioned above, the sorting algorithm is pdqsort, which does not use recursion. Therefore, the solution is robust to large inputs. The only limitation is the available memory. If the input is too large to fit into memory, the algorithm will fail. However, this is not a problem of the algorithm itself, but rather a problem of the system it is running on. The algorithm could be extended to handle large inputs by using a streaming approach. The algorithm would then read the input in chunks, sort the chunks and merge them. This would allow the algorithm to handle large inputs without running into memory issues. However, this would also increase the runtime complexity of the algorithm.