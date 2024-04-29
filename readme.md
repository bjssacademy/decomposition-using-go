# Decomposition

Decomposition is like having a big puzzle and deciding to tackle it one section at a time. Instead of staring at the entire puzzle and feeling overwhelmed, you separate it into smaller sections. You might start with the edges, then work your way towards the middle, or focus on one particular part that looks easier.

Similarly, when you decompose a problem in coding, you're taking a complex task and breaking it down into smaller, more understandable steps. Each step is like a smaller piece of the puzzle that you can work on and understand more easily. By breaking it down, you can tackle each piece separately, which makes the overall problem much easier to solve.

Easier said than done, eh? Well let's have a look at a problem:

## Problem

Given a 0..N length two-dimensional array of integers, with 0...N integers in each subarray, return the largest sum possible from a single subarray.

### Example

Given the array `[[10,2,-5],[0],[5,5,3]]` you should return 13, as the largest sum in any subarray is 13 (5+5+3).

### Code Starter

```go
package main

func Solution(A [][]int) int {
    return 0
}
```

## First Thoughts

My first thoughts are "do I understand what all thw words mean?"

Key points:
1. A two dimensional array is an array of arrays.
2. Each "subarray" contains `0..N integers`.
3. `0..N` means "none, or any number up to infinity"
4. This means subarrays can be empty or contain 1 integers or 2 integers or 3 integers...or any number of integers. I won't know in advance.
5. I need to total all those integers in each sub array together.
6. Whichever subarray has the highest total is the answer I need.
7. There is a worked example, which tells me the integers can be negative as well as positive.

## First Steps

The first thing I do is *run the code*.

```go
package main

func Solution(A [][]int) int {
    return 0
}
```

> We'll assume that there are no automated tests at this point, so we'll have to write our own. Many online systems will have a limited set of tests that will run so you can check your code.

Okay, so the code compiles, but nothing useful happens. Fortunately we've got a worked example we know should be right, so let's add a test, and make sure we can see that in our function:

```go
package main

import (
	"fmt" //added this import
	"testing" // added this import
)

func Solution(A [][]int) int {
	fmt.Println(A) //added this line
    return 0
}

//added this test to call the function
func TestExample(t *testing.T) {
	testData := [][]int{{10, 2, -5}, {}, {5, 5, 3}}
	Solution(testData)
}
```

Okay, so now we've got a slice to send to our function, which will just print it out:

```
=== RUN TestExample
[[10 2 -5] [] [5 5 3]]
--- PASS: TestExample (0.00s)
PASS
```

Great, so we can see how it works now, and we can call our function from our test. But our test passes. Let's fix that by asserting the result we *want* our function to return - 13.

```go
func TestExample(t *testing.T) {
	testData := [][]int{{10, 2, -5}, {}, {5, 5, 3}}

	result := Solution(testData) // assign to new variable

	expected := 13 // the answer we want back from our function

	if result != expected {
		t.Errorf("Answer we got %d; want %d", result, expected)
	}
}
```

Now we run our test:

```
=== RUN TestExample
[[10 2 -5] [] [5 5 3]]
    line 21: Answer we got 0, want 13
--- FAIL: TestExample (0.00s)
FAIL
```

Excellent news - our test works, and fails correctly! Now we can get back to the function, which at the moment looks like this:

```go
func Solution(A [][]int) {
	fmt.Println(A) 
    return 0
}
```

Looking back at my first thoughts and printing out the slice that it passed to the function, I know that it wants the number 13 back. In my example I know there are *exactly* 3 subarrays, and it's the third one that will yield the highest number when all of the ints in the subarray are added together.

So I know I'm going to need some sort of "total" to return, so I create that variable:

```go
func Solution(A [][]int) {
	
    total := 0

    return total
}
```

Next, I know the highest subarray is the third one (or index position 2), and I know it has three integers in that array at positions 0, 1 and 2.

What's the simplest thing I can do? Well, probably something like this:

```go
func Solution(A [][]int) {
	
    total := 0

    total += A[2][0] + A[2][1] + A[2][2]

    return total
}
```

And do you know what, that passes the test! Great success, teas an biscuits all round!

The code is short enough to be understandable, even if it is a bit ugly.

Now I go back to some of my first thoughts and see what else I understand.

Oh...wait *Each "subarray" contains `0..N integers`.*

So there won't always be exactly 3 integers, there could be 2 or 4, or 10, or 20.

## Dealing with 0...N

Let's write another test that fails and has four integers in the third subarray that should total 20:

```go
func TestFourIntegers(t *testing.T) {
	testData := [][]int{{10, 2, -5}, {}, {5, 5, 5, 5}}

	result := Solution(testData)

	expected := 20

	if result != expected {
		t.Errorf("Answer we got %d; want %d", result, expected)
	}
}
```

Run the test, and we'll see that we have a failure.

```
--- PASS TestExample
=== RUN TestFourIntegers
    line 21: Answer we got 15, want 20
--- FAIL: TestFourIntegers (0.00s)
FAIL
```

That's because this line:

```go
total += A[2][0] + A[2][1] + A[2][2]
```

*only* sums the first three items in the subarray.

Okay, so I know a subarray in this instance is actually a slice of ints. And I won't know in advance exactly mow many ints there are in this slice.

And I also know a slice of ints is a collection, and collections are iterable.

Aha! I can range over a collection! So I can replace the line that sums the total with a for loop:

```go
    for _, number := range A[2] {
		total = total + number
	}
```

I run my tests and....success!

## Refactoring 2

I'm going well here, I can sum a slice at position 2 in a 2D array. However, my code *only* works if the biggest sum is in the third slice. What happens if the biggest slice is in the fourth, or fifth?

Well, let's write a test.

```go
func TestFourSubArraysWithLargestLast(t *testing.T) {
	testData := [][]int{{10, 2, -5}, {}, {5, 5, 5}, {10, 10, 10}}

	result := Solution(testData)

	expected := 30

	if result != expected {
		t.Errorf("Answer we got %d; want %d", result, expected)
	}
}
```

Okay, so now our 2D array conatins four subarrays, and the fourth one is the largest. If we run our tests again, we'll see this test fails.

Okay, so how do we fix this? Well, if we knew how many subarrays there were, we could do the summing for each subarray.

We could use the `len()` function to get the number of subarrays and then use a three-component loop:

```go

    subarrayCount := len(A)

    for i := 0; i < subarrayCount; i++ {
	    fmt.Println(A[i])  //print the curent subarray
    }

```

And we could then move our existing "summing" loop inside that, replacing the number 2 in `A[2]` with our variable `i`:

```go

    subarrayCount := len(A)

    for i := 0; i < subarrayCount; i++ {
	    fmt.Println(A[i])
        for _, number := range A[i] {
            total = total + number
        }
    }

```

Right, let's run our tests.

Oh dear, bad news, now *everything* is failing. What's going on?

Well, have a look at the error output for TestFourSubArraysWithLargestLast:

```
Answer we got 52; want 30
```

52? How are we getting 52?

Let's look at our input:

```go
testData := [][]int{{10, 2, -5}, {}, {5, 5, 5}, {10, 10, 10}}
```

Hmm, we should just be getting 30. I mean if you add *all* those numbers up you get 52, but we only want the largest subarray total, not the total of everything in the array itself.

Time to go and debug.

## Refactoring 3

Our function currently looks like this:

```go
func Solution(A [][]int) int {

	total := 0

	subarrayCount := len(A)

	for i := 0; i < subarrayCount; i++ {
		fmt.Println(A[i])
		for _, number := range A[i] {
			total = total + number
		}
	}

	return total
}
```

It's currently not quite clear where we are in the loop or what we are doing, unless you can mentally keep in your head what `i` means, and which index of `A` you are using.

Let's start by introducing an intermediate or explaining variable:

```go
func Solution(A [][]int) int {

	total := 0

	subarrayCount := len(A)

	for index := 0; index < subarrayCount; index++ {
		
        currentSubarray := A[index] // new explaining variable
        
        fmt.Println(currentSubarray)

		for _, number := range currentSubarray {
			total = total + number
		}
	}

	return total
}
```

Okay, now it's a bit clearer that we are operating on the subarray, which allows us to read the code a bit more easily.

However, we still have `A`. Fortunately, since we are in charge of the function signature naming, we can change that. Let;s do that now so we can see more clearly what's going on.

```go
func Solution(input [][]int) int {

	total := 0

	subarrayCount := len(input)

	for index := 0; index < subarrayCount; index++ {
		
        currentSubarray := input[index] // new explaining variable
        
        fmt.Println(currentSubarray)

		for _, number := range currentSubarray {
			total = total + number
		}
	}

	return total
}
```

Okay, now it's a *bit* easier to see where we are and what we are acting on. So, why is our total 52 rather than 30?

---

Oh, we're adding everything together from every subarray. We don't want that, we need a way to check if the *current* subarray total is *higher* than our highest total so far.

Let's introduce a new variable for this subtotal:

```go
func Solution(input [][]int) int {

	total := 0

	subarrayCount := len(input)

	for index := 0; index < subarrayCount; index++ {
		
        currentSubarray := input[index] // new explaining variable
        
        fmt.Println(currentSubarray)

        subtotal := 0
		for _, number := range currentSubarray {
			subtotal = subtotal + number
		}

	}

	return total
}
```

Good, so now the scope of `subtotal` is within the for loop, and contains only the total of the current subarray.

But we no longer update total, so all our tests fail! We need to return the highest subtotal we find:

```go
func Solution(input [][]int) int {

	total := 0

	subarrayCount := len(input)

	for index := 0; index < subarrayCount; index++ {
		
        currentSubarray := input[index] // new explaining variable
        
        fmt.Println(currentSubarray)

        subtotal := 0
		for _, number := range currentSubarray {
			subtotal = subtotal + number
		}

        if subtotal > total { 
            total = subtotal
        }

	}

	return total
}
```

Let's check our tests! Which should now all pass.

## Refactoring 4

Okay, so it looks like we now return the subarray with the highest sum. But the code still feels a bit clunky and hard to read. Using the index of the subarray isn't great as we don't even use it, so we can simplify our for loop and move out `currentSubarray` variable:

```go
func Solution(input [][]int) int {

	total := 0

	for _, currentSubarray := range input {

        subtotal := 0
		for _, number := range currentSubarray {
			subtotal = subtotal + number
		}

        if subtotal > total { 
            total = subtotal
        }

	}

	return total
}
```

Okay, this looks a bit cleaner. Now we can start working on the things we haven't looked at, such as "what if the array is empty?" and "what if all the subarrays are empty?"

# A More Complex Starter

Now let's take a different problem:

> Given a string as your input, perform the calculation to assert the final total.
>
> For example:
>
> 1. Given the string "1 + 5" the answer would be 6
> 2. Given the string "5 - 1" the answer would be 4
> 3. Given the string "2 * 5" the answer would be 10
> 4. Given the string "10 / 2" the answer would be 5
>
> Strings may contain multiple operators, in any order, and multiple numbers.
>
> You may assume that the input is valid and will start and finish with a number rather than an operator, and that there will always be at least one operator.
>
> Your starter code is included below.
>
> ```go
> package main
> 
> func Solution(S string) int {
>     return 0
> }
> ```

What are you first thoughts?

## My First Thoughts

My big thought here is that this is a *calculator*. But calculators work on numbers and I have a string. Hmm.

1. There are four operators that are possible: 
- \+ (add)
- \- (subtract)
- \* (multiply)
- \/ (divide)
2. The input is provided as a string, but I need to make decisions based on those 4 operators - sounds like a *condition*.
3. The string is *space delimited* - that is, there is a space between each number and operator.
4. The input is provided as a string, therefore I will need to convert the non-operators (the numbers) to integers
5. There could be multiple operations, so "1 * 2 + 3" should equal 5
6. The input is always going to be valid, so I don't have to check for empty strings, or deal with invalid calculations.

## First Steps

I'm going to write a test. The simplest test I can think of right now is adding two numbers:

```go
func TestAddition(t *testing.T) {

	testData := "1 + 1"
    expected := 2

	result := Solution(testData)

    if result != expected {
        t.Errorf("Expected %d, but got %d", expected, result)
    }

}
```

Okay, so now when I run this, it fails because our Solution function just returns 0 every time. Let's go fix that.

Because one of my first thoughts was that the string is *space delimited*. This means I can [split](https://www.geeksforgeeks.org/how-to-split-a-string-in-golang/) the string up by spaces and put it into an array that I can then iterate through and see if the current item is a number or an operator.

Let's start at the beginning, and split our input:

```go
package main

import (
	"fmt"
	"testing"
    "strings"
)

func Solution(S string) int {
    
    operations := strings.Split(S, " ")

}

//.. rest of our test code

```

Now we have a variable, `operations` which is an array - a slice in Go. 

And since we are doing the simplest thing, we know that there are exactly three items in the array, so we can access them directly by their index:

- `operations[0]` gives us "1"
- `operations[1]` gives us "+"
- `operations[2]` gives us "1"

I can see that the operation at index [0] and [2] return a string. I can't add strings together, so I need to convert them into integers. A quick search reveals the [built in strconv](https://www.geeksforgeeks.org/strconv-atoi-function-in-golang-with-examples/) package.


```go

import "strconv"

func Solution(S string) int {
    
    operations := strings.Split(S, " ")

    firstNumber, _ := strconv.Atoi(operations[0])
    secondNumber, _ := strconv.Atoi(operations[2])

    return firstNumber + secondNumber


}
```

Okay, that should pass our test. Let's run it and see...

## Our Next Test

Of course, our test passes, but only if we need to add two numbers together. Let's write a test to see what happens if we need to subtract:

```go
func TestSubtraction(t *testing.T) {

	testData := "2 - 1"
    expected := 1

	result := Solution(testData)

    if result != expected {
        t.Errorf("Expected %d, but got %d", expected, result)
    }

}
```

When we run our test now, we see that it has failed. Looking at our solution now we see that we are only handling *if* the operator is "+", so we need some way to check what the operator is. Let's use an if statement to start:

```go

import "strconv"

func Solution(S string) int {
    
    operations := strings.Split(S, " ")

    firstNumber, _ := strconv.Atoi(operations[0])
    secondNumber, _ := strconv.Atoi(operations[2])

    if operations[1] == "+" {
        return firstNumber + secondNumber
    } else {
        return firstNumber - secondNumber
    }

}
```

Excellent, now we run our tests and they all pass. Joyous day! But we're only testing 2 of the operators we haven't checked for the multiply or divide operators.

## Multiple and Divide 

So we add two more failing tests:

```go
func TestMultiplication(t *testing.T) {

	testData := "2 * 5"
    expected := 10

	result := Solution(testData)

    if result != expected {
        t.Errorf("Expected %d, but got %d", expected, result)
    }

}

func TestDivision(t *testing.T) {

	testData := "20 / 4"
    expected := 5

	result := Solution(testData)

    if result != expected {
        t.Errorf("Expected %d, but got %d", expected, result)
    }

}

```

Let's get back to our code. Now, we could write a lot if `if..else if` statements, but fortunately we know about the [switch](https://github.com/bjssacademy/go-switch) statement:

```go

func Solution(S string) int {
    
    operations := strings.Split(S, " ")

    firstNumber, _ := strconv.Atoi(operations[0])
    secondNumber, _ := strconv.Atoi(operations[2])

    switch operations[1] {
        case "+":
            return firstNumber + secondNumber
        case "-":
            return firstNumber - secondNumber
        case "*":
            return firstNumber * secondNumber
        case "/":
            return firstNumber / secondNumber
    }

}
```

Good news, all our tests pass!

## Dealing with multiple operators

The good news is we now have a very basic calculator. The less good news it that it only works if there are exactly three operations.

Here's an example of an arithmetic operation that it won't solve: "1 + 1 + 1". Our solution will currently return 2 rather than the correct answer of 3.

Now, my guess is that your first thought is "Since arrays are iterable, we can iterate over it and check whether the item at each step is a number or an arithmetic operator."

Okay, let's see where that leads us.

First, let's set up our iterator, and print out what each operation is:

```go
func Solution(S string) int {
    
    operations := strings.Split(S, " ")

    for _, operation := range operations {
        fmt.Println(operation)
    }

}
```

Which produces the output:

```
1
+
1
```

Excellent, I can now see that the value in operation is "1", then "+", then finally "1".

Now I need to decide what to do with each operation. Fortunately I have this code already in my switch statement, and I can add a default case statement to deal with anything that isn't and arithmetic operator:


```go
    for _, operation := range operations {
        switch operation {
            case "+":
                return firstNumber + secondNumber
            case "-":
                return firstNumber - secondNumber
            case "*":
                return firstNumber * secondNumber
            case "/":
                return firstNumber / secondNumber
            default:
                //convert the number
        }
    }
```

Oh wait. That's not right. Now that I am iterating over the entire array, I need to store the operator until I need it, which is when I get the next item in the array. At the moment I don't know the index.

I could refactor to use a three component loop so I have the index:

```go
for i := 0; i < len(operations); i++ {
	
}
```

Now I can add in the switch, store the operator in a temporary variable, and then convert the numbers using the last operator, null the numbers out and carry the total...

Hang on, this is getting a bit complicated. I'm not looking at the problem right. There must be a simpler way.

## Reframing the problem

My coding brain has got in the way. I have seen an array and immediately thought "loop!". But let's look at the very simple equation we need to solve:

> 1 + 1 + 1

What's common here? In fact, if I write out more examples, what patterns can I see?

> 1 + 1 + 1 = 3
>
> 2 + 1 + 5 + 7 = 15
>
> 2 - 2 + 5 - 2 + 3 = 5

I break the problem apart a bit more and do the sum from the left to the right:

> (1 + 1) = 2
>
> (2 + 1) = 3

Now I look at my code I can see this is actually the code I already have. If I have 2 numbers and an operator, my code will work! How can I use this?

I need to keep a running total somehow. But my first addition has three parts, and the second I only need 2 parts (the operator and the next number).

So something like:

```go
1 + 1 + 1 //our string
(1 + 1) + 1 //chunked up
(operations[0] operations[1] operations[2]) operations[3] operations[4] //indexes
```

But if I think of the problem in another way:

```go
((1) + 1) + 1
```

Then if I *start* my loop at position 1 instead of zero, and step (increment) by 2 instead of 1, I will always be getting the arithmetic operator, and since I know wherever there is an operator the next index up `[i+1]` will be a number....

```go
func Solution(S string) int {

	operations := strings.Split(S, " ")

	firstNumber, _ := strconv.Atoi(operations[0]) //convert the first operator to an integer

	total := firstNumber //assign the first integer to the total

	for i := 1; i < len(operations); i += 2 { //start the loop at index 1, and increment i by  2 instead of 1 (the last part of the three component loop is `1 += 2` rather than `i++`)
		secondNumber, _ := strconv.Atoi(operations[i+1])
        switch operations[i] {
            case "+":
                total += secondNumber
            case "-":
                total -= secondNumber
            case "*":
                total *= secondNumber
            case "/":
                total /= secondNumber
		}
	}

	return total

}
```

What I have done here, almost by mistake, is to recognise that the arithmetic operator is always at an odd position (index 1, 3, 5 etc).

## Other options

Some people might see the problem and think a recursive function would be the best approach. You could try and use a stack and every time you have 3 items on the stack perform the operation. That would look something like this:

```go
func Solution(S string) int {
	operations := strings.Split(S, " ")
	stack := []string{}

	for _, op := range operations {

		stack = append(stack, op)

		if len(stack) == 3 { // if there are enough items on the stack to perform an operation
            //assign from stack
			num2 := stack[len(stack)-1]
			op := stack[len(stack)-2]
			num1 := stack[len(stack)-3]

			stack = stack[:len(stack)-3] //empty the stack
			result := strconv.Itoa(applyOperation(op, num1, num2))

			stack = append(stack, result) // Push the result onto the stack

		}
	}

	total, _ := strconv.Atoi(stack[0]) // The final result will be the only number left on the stack
	return total
}

func applyOperation(op, first, second string) int {
	num1, _ := strconv.Atoi(first)
	num2, _ := strconv.Atoi(second)
	switch op {
        case "+":
            return num1 + num2
        case "-":
            return num1 - num2
        case "*":
            return num1 * num2
        case "/":
            return num1 / num2
	}
	return 0
}
```

