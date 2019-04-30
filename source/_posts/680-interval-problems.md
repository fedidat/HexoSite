---
title: 'Algorithms: interval problems'
date: 2019-04-15 09:46:21
tags:
---

These are 3 interval problems that I have been asked by different companies in my programming interviews. I'll present the problem, a possible candidate-interviewer exchange, and naive and efficient solutions with test cases.

## 1. First non-overlapping interval

The question goes this way:

>You have a list of licenses for a product. Find the earliest year which is not covered by an unexpired license.

### Clarifying:

- **Candidate**: Is there a minimum of licenses? And isn't MIN_INT always correct?
  
  **Interviewer**: What do you think?

  **Candidate**: Well, if there are no licenses, it is trivially possible to return MIN_INT. And I suppose you meant the earliest year past the earliest start date.

  **Interviewer**: Right.

- **Candidate**: How are dates represented? And are intervals inclusive?
  
  **Interviewer**: What do you think?

  **Candidate**: `How about (start, end)`? And for simplicity let's say intervals are indeed inclusive, so `(2000,2000)` covers exactly one year.

  **Interviewer**: Sounds good.

- **Candidate**: How about an example? Say we have:

      [(2011, 2015),
       (2021, 2022),
       (2014, 2018),
       (2030, 2035),
       (2019, 2019)]
      Result: 2020

  **Interviewer**: Sounds good.

### Naive solution

Go over each interval `(start, end)`, and insert each of the `end - start` elements between `start` and `end` in a list. Then, sort that list in increasing order. Finally, iterate that list and return the first number `n` not preceded by `n-1`. Complexity can be `O(2^n * log(2^n)) = O(2^n * n)` to sort the resulting list in the worst case, as intervals get larger and sparser.

### Efficient solution

In a similar spirit to the naive solution but acting directly on the intervals, sort the list of intervals by increasing `start`, then iterate over it. If at any point `current.start > previous.end + 1` we may return `previous.end + 1` as the solution.

A quick Java solution with test cases may look like this:

```java
public class MinNoOverlap{
    public int minNoOverlap(int[][] intervals) {
        if(intervals == null || intervals.length == 0)
            return Integer.MIN_VALUE;
        Arrays.sort(intervals, (a, b) -> a[0] - b[0]);
        int currentEnd = intervals[0][1];
        for(int i=1; i < intervals.length && intervals[i][0] <= currentEnd + 1; i++)
            currentEnd = intervals[i][1];
        return currentEnd + 1;
    }

    public void minNoOverlapExample() {
        assert(minNoOverlap(new int[][]{{2011,2015}}) == 2016);
        assert(minNoOverlap(new int[][]{{2011,2015},{2014,2018},{2019,2019}}) == 2020);
        assert(minNoOverlap(new int[][]{{2011,2015},{2021,2022},{2014,2018},{2030,2035},{2019,2019}}) == 2020);
    }
}
```

Complexity is `O(n*log(n)) (sort) + O(n) (iterations)`, so `O(n*log(n))`.

### Easy optimization

If looking for an optimization, you can remark that, since we sort the intervals based on limited integer values, we can use base-N radix sort and decrease the complexity down to a minimum of `O(n)` amortized linear time. In fact, your JVM's implementation of `Arrays.sort` is probably already doing just that.

For reference, this [SO question](https://stackoverflow.com/q/19300735) talks about it.

## 2. Maximum sum of concurrent overlaps

The question goes this way:

> You are a critical TV cable service, with various qualities and formats for different channels. These channels only run at certain times of the day. You need to talk to a PHY cable provider service to get a guarantee for sufficient bandwidth for your customers at all times. How would you determine the necessary bandwidth?

### Clarifying

- **Candidate**: Let me write an example to see if I have the requirements down. I will be organizing the data in a list of 3-tuples in the format `(start_time, end_time, bandwidth)`, with bandwidth in, say, Mbps, and times in hours between 0 and 24. So an example would be:

      [(2, 16, 10),
       (1, 5, 20),
       (10, 12, 25),
       (20, 22, 30)]

  ... where the first tuple indicates 10Mbps between 02:00AM and 4:00PM. 
  
  In this instance, we reach a peak of `10 + 25 = 35 Mbps` between 10AM and 12PM.

  **Interviewer**: Sounds fine.

### Naive solution

Let's start at the most basic solution: Get an array of hours `[0, ..., 24]`, and for each of them scan the intervals and get the total bandwidth being consumed at that time.

This doesn't work for infinite time precision and is very inefficient for high precision. But from here we can guess better solutions.

### Interval-oriented solution

In many intervals problems, we can think in terms of breaking down intervals into smaller ones. So we can start with one interval `[(0, 24, 0)]` (0 Mbps between 00:00 and 23:59), then iterate over the input, integrating each new interval:

    [(0, 24, 0)] 
    -> (2, 16, 10) => [(0,2,0), (2,16,10), (16,24,0)]
    -> (1, 5, 20) => [(0,1,0), (1,2,20), (2,5,30), (5,16,10), (16,24,0))]
    -> (10, 12, 25) => [(0,1,0), (1,2,20), (2,5,30), (5,10,10), (10,12,35), (12,16,10), (16,24,0)]
    -> (20, 22, 30) => [(0,1,0), (1,2,20), (2,5,30), (5,10,10), (10,12,35), (12,16,10), (16,20,0), (20,22,30), (22,24,0)]

This is tricky to implement efficiently, however, because every time we integrate another interval from the input, we have to find which interval we should break into two or more. We can either order the input list, or binary search the output list for intervals to break, but for that effort, we can only get to O(n * log(n)), whether .

### Efficient solution: event-driven

We can see that what we really need is some variable that iterates over the input and keeps track of the maximum concurrent bandwidth so far. In order to do that, it would be a good idea to process increases and decreases separately, and modify the current concurrent bandwidth accordingly.

So we will rearrange each interval `(start_time, end_time, bandwidth)` into two events `(start_time, bandwidth)` and `(end_time, -1 * bandwidth)`. Then we will sort the events by increasing `start_time`. Now we are ready to iterate over the input, keeping track of both the current and maximum values of the concurrent bandwidth.

A quick Java solution with test cases may look like this:

```java
public class MaxConcurrentSum {
    public static class Interval {
        public int start, end, bw;
        public Interval(int _s, int _e, int _b) { start = _s; end = _e; bw = _b; }
    }
    private static class Event {
        public int time, bw;
        public Event(int _t, int _b) { time = _t; bw = _b; }
    }

    public int maxConcurrentSum(List<Interval> intervals) {
        List<Event> events = new ArrayList<>();
        for(Interval interval : intervals) {
            events.add(new Event(interval.start, interval.bw));
            events.add(new Event(interval.end, (-1) * interval.bw));
        }
        Collections.sort(events, (a, b) -> a.time - b.time);
        int currentBw = 0, maxBw = 0;
        for(Event event : events) {
            currentBw += event.bw;
            maxBw = Math.max(maxBw, currentBw);
        }
        return maxBw;
    }

    public void maxConcurrentSumExample() {
        assert(maxConcurrentSum(Arrays.asList(new Interval[]{new Interval(2,16,10), 
                new Interval(1,5,20), new Interval(10,12,25), new Interval(20,22,30)})) == 35);
    }
}
```

The complexity here is `O(n * log(n))` due to the event sorting.

This [Geeks for geeks article](https://www.geeksforgeeks.org/find-the-point-where-maximum-intervals-overlap/) talks about this problem and proposes the use of an auxiliary array with a value for each number between the minimum and maximum times. This array stores the value of every event, which we then iterate with the current and max bandwidth variables. This replaces the need for sorting, but does not support high precision. So this is in principle `O(n)` complexity, but potentially `O(2^n)` for very large intervals.

Another solution could involve using an [interval tree](https://en.wikipedia.org/wiki/Interval_tree) (a tree that holds intervals), which takes `O(n * log(n))` to build, but only `O(log(n))` to query for overlaps. This offers the same complexity but may provide shorter actual run time.

### 3. Variant: Overlap history

A variant of the previous problem, which I was also asked, goes this way:

> You are the maintainer of a phone service. You have a log of calls including, for each call a tuple of `(start_time, end_time)`. How do you process this log to fetch a history of the number of concurrent calls?

The previous problem's solution can also work here, but instead of updating the maximum number of overlaps, we write the current number every time it changes.