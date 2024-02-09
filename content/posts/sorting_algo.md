---
title: "Quick sort and bubble sort time complexity"
date: 2024-02-08T14:40:48+02:00
draft: false
toc: true
math: kalex
images:
tags:
  - sorting_algorithm
  - algorithm
  - time_complexity
  - big_O
  - Python
---


  This week I was tasked to implement bubble sort and empirically calculate its time complexity.
  But first what even is time complexity?

  ## Time complexity

  In computer science, *computational complexity* indicates the amount of resources an algorithm needs to execute.
  The are two types of complexities:

  - time complexity (computation time)
  - space complexity (memory storage requirements)

In this post, I will only focus my attention on the former.

Time complexity provides an estimate of the amount of time an algorithm takes to run as a function of the size of its input.
This estimate supposes that each elementary operation takes a fixed amount of time.
Thus, the amount of time taken by the algorithm can be related by a constant factor.

Time complexity is often expressed using big O notation, 
which describes the upper bound on the growth 
rate of an algorithm's running time as the size of the input increases.

### Big O notation

Big O notation is a notation that describes the limiting behavior of an asymptotic growth rate
of a function or an algorithm.
It provides a way to classify the efficiency of algorithms in terms of their worst-case, best-case or average-case performance.

To put it formally:

$$
{\displaystyle f(x)=O{\bigl (}g(x){\bigr )}\quad {\text{ as }}x\to \infty }
$$

- $f$ is the function we want to be estimated.
- $g$ is the comparison function.
- both functions are defined on some unbounded subset of real positive numbers $(+1,+2,+3,+4, ...+\infty )$.
- $g(x)$ is stricly positive for all large numbers of $x$.


Now that we have a more formal explanation of Big O let's look at this graph I found online:

![big_o_plane]We can see that as the number of elements increases, the growth rate stays constant but depending on our
constant, the algorithm might scale linearly $O(n)$, others logarithmically $O(\log(n))$, and some even exponentially $O(n!)$.



## Big O and algorithms

Now that we have a general idea of what Big O let's see how we can apply it to algorithms so that we can quickly assess the time complexity
of an algorithm.

Before going into bubble sort it's better to cement the relationship between Big O and an algorithm.

So to do that let's make a simple function that counts the even numbers inside an array.

```python
def find_even_numbers(data: List[int]) -> int:
    counter = 0
    for i in data:
        if i % 2 == 0:
            counter += 1
    return counter
```

This function function iterates through the entirety of the list, if it finds an even number
`if i % 2 == 0:` then it will increment our counter.

Because we are iterating over our entire array using our for loop, we can safely assume that 
our running time is $O(n)$.
But let's look at a graph.

To do that we first need an array of values and an array to store the times.


```python
time_complexity = []
values = [1000, 5000, 10000, 50000, 100000, 500000, 1000000, 2000000,  3000000, 5000000]

for n in values:
    data = [random.randint(1, 1000) for i in range(n)]

    start_time = time()
    find_even_numbers(data)
    end_time = time()

    print("number finder time", end_time - start_time)
    time_complexity.append(end_time - start_time)

plt.plot(values, time_complexity, label="time complexity")
plt.grid(True)
plt.legend()
plt.show()
```


To empirically calculate the time complexity we create a list comprehension that iterates through the various values inside or array and creates
an array of random integers of that specific size.

We then get the starting timestamp before and after executing the function and calculate the difference.
To plot everything we use matplotlib to create a plot and pass the array size as the X axis and the different timing as the Y axis.

This is the result:
![plot_o(n)](/plot_o(n).webp)

pretty linear.
As the array size increases so does the time required to calculate the total amount of even numbers in that list.

## Bubble Sort

Now that we have a basic understanding of time complexity and big O let's look
at bubble sort.

Bubble sort is a very simple sorting algorithm.
It gets its name because out of every pass, the highest number in the list always reaches the top like a bubble reaching the surface of the water.

The algorithm is pretty simple.
- Iterate through the list
- if the first number is bigger than the one next to it, switch the place
- if the number is smaller ignore them and go to the next pair of number
- rinse and repeat for the length of the array - 1 times.

### Example

Let's give an example:

If we have this list, [1, 5, 7, 3]

What bubble sort does is this:

{{< image src="/1_pass_bubble.png" alt="first_pass_bubble" position=" center" style="border-radius: 8px;" >}}

because the array is 4 units big, we are going to iterate over this array 4 - 1 = 3 times.
The image shows what happens during the first pass.

- We start from the beginning of the array at position `i = 0`.
- We then get the element in the position `i + 1`.
- We compare if `i > i + i`.
In this, we see that at position `i=0`  we have `1` and at position
`i + 1` we have the value 5.

Now we check if `1 > 5, since it's not we don't do anything and move to the next pair.

In the case where `i = 1` the same thing happens.

In the case of `i = 2` we see that in position `i = 2` we have the value `7` and in position
`i + 1` we have the value 3.
So in this case the comparison is true and so we switch them place.
At this point, we finished the first iteration of the algorithm and now we have the value `7` at the very last place of our
array.

Let's look at the second pass:

{{< image src="/2_pass_bubble.png" alt="second_pass_bubble" position=" center" style="border-radius: 8px;" >}}

Because we already sorted the first biggest number there is no need to iterate over it, so we are going to 
have just two iterations.

Just like before we see that at position `i=0`  we have `1` and at position
`i + 1` we have the value 5.
So again no switch.

At position `i = 1` we have `5` and at position `i + 1` we have the value `3`.
In this case, we switch.
At this point, we have reached the end of the unsorted part of the array so we can move on to the last iteration of the array, remember *4 element array, 3 iterations*.

Now the last pass of our algorithm.

{{< image src="/3_pass_bubble.png" alt="third_pass_bubble" position=" center" style="border-radius: 8px;" >}}


This last part is already sorted,
so we check if `1 > `3`, if it's not, we reach the end of the unsorted part of the array, and now the whole array is sorted.

### Code Implementation

Let's implement this in the code now.

```python

def bubble_sort(data):
    for i in range(len(data) - 1):
        for j in range(len(data) - 1 - i):
            if data[j] > data[j + 1]:
                data[j], data[j + 1] = data[j + 1], data[j]
                
    return data
```

That's it!

What we are doing here is:
in the first for loop `for i in range(len(data) - 1):`
we want to iterate over the entire array size - 1.
The reason why we doing it one less time than the array length is that when we do the n - 1 pass we have already 
sorted the array, so there is no need to do another pass.

The second nested loop `for j in range(len(data) - 1 - i):`
Is the one that iterates over each element inside the array.
To put it simply the images in the example represent an iteration of the first loop, while 
the actual comparison represents an iteration of the nested loop.
(In retrospect I should have used j in the pictures to make it more clear, but oh well!).

Inside the for loop, we have this if statement, `if data[j] > data[j + 1]:`
that checks if the value in potion `j` is bigger than the value in position `j + 1`.
This last line `data[j], data[j + 1] = data[j + 1], data[j`]` simply swaps the two values.


The time complexity of bubble sort is $O(n^2)$
Let's see why:

1. In the worst-case scenario, for each element in the list, bubble sort needs to compare it with every other element in the list. This results in 
    $n$ comparison for the first item in the list, $n - 1$ for the second item, $n- 2$ for the third and so on until 1 comparison for the second-to-last element.
$$
  (n-1) + (n-2) + (n-3) + ... + 2 + 1
$$
By using the sum of natural numbers formula we can rewrite the previous equation like this:
$$
  \sum_1^n{\frac{n(n+1)}{2}}
$$

Due to the nature of Big O, we drop constants and remove the sum so the formula becomes this:

$$
  n(n+1)
$$

Let's calculate this:

$$
n^2 + n
$$

And now because in Big o we can drop non-significant values we can say that:
$$
  n^2 + n \backsim n^2
$$

The reason why in Big O we can do that is because given a big enough value of $n$ the the $ +n$ inside $n^2 +n $ doesn't really matter.


## Quick sort

