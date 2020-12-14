---
layout: post
title: Advent of Code 2020 (Week 2)
---

Notes on the second week of the 2020 Advent of Code challenge at
https://adventofcode.com/

Check out the [previous post]({% post_url 2020-12-04-advent-of-code %}) for details on the challenge.

# Day 8

Great start to the week with a simplified instruction set puzzle.

Initially my thought is "only jump instructions matter"
until the solution requires the value of the accumulator at the
point of the second execution of an instruction.
So we need to write a simple machine code interpreter to run the code
and provide the value of the accumulator when a loop is detected.

Part 2 is much more interesting - we need to find an instruction that
when flipped from nop to jmp (or vice versa), will result in the
program completing.

For reference - this is a simplified version of a famous problem in
computer science called "the halting problem" - the reason why this
is simplified is that we have a fixed input to the program (an accumulator containing 0)
and the instruction set has no operation that is dependent on the value of the
input.

# Day 9

A dive into almost-cryptography for today

The first task is to create moving window the size of the preamble (5 in test data, 25 in puzzle)
then for every new number, we can use our day 1 code to find a tuple within it that
matches - and when it doesn't we've found the number for this part.

The second task builds on the first - the challenge here is to process the
input with a variable size moving window, where the cumulative sum across the Window
could contain the target number
after each step - adding or removing a number to the window
we need to check if the windows total sum exceeds the target number
and that there is no cumulative sum
from the start of the window that is the target, is so we can safely drop the first
number and recalculate the cumulative sums within the window
if there is no match within the window - then we add the next number.

Even when we find a match, we're not finished - so we have a window that contains
the numbers summing to the target number - we need to sum the min and max of those
numbers.

This is a little bit like trying to brute force a private key knowing the public key
and a large amount of cyphertext.  Obviously in a real brute force attempt, the
cyphertext doesn't just contain the two prime numbers that are the source of the
private key - but the principal is similar.   This is why we use large keys kids....

# Day 10

Back to basics today.

This puzzle is stated in a very complicated way, but overall we need to
use every adapter in the list - to do that we have to go in order from
the lowest rating to the highest.
Then we can reduce that list to a count of differences of one and three
and multiply them.  Remembering to add one for the
last adapter to device step.

The second part is more complex - we need to work out the number of
permutations of possible sequences.

We could build a tree from the choices, and count the leaf nodes of the tree; or
we could let our language stack manage the tree and use recursion.
Both of these options suck from an efficiency perspective.
Now we could improve that by memoizing the recursive function so it only
needs to calculate each path once even though we traverse the whole tree.

I went through quite a few different ideas at this point - making a digraph
of the sequences and counting the digraph paths for instance.

But then I had the realisation that it's a progressive pattern - if you discard
the largest adapter, the puzzle is the same, and adding it back on adds a number
of paths on depending on the paths to the final one two or three numbers.

Reversing this idea, and processing the list in order (starting with the 0 jolt socket) we can
maintain a count of "permutations to get here" - and add the current count
to the one, two or three next steps.   Then the count we have on the last
step is the answer.

# Day 11

Today we have a cellular automata, which is an initial state of cells
(the input) and a set of rules for mutating the cellular state.
The most famous cellular automata is Conway's Game of Life (sadly John Conway himself died of
complications due to COVID-19 this year)

The problem set of rules will result in a stable equilibrium in the state, so we
have to process the state iteratively until there are no more changes.

Because the next state depends on the previous state with each cell changing
simultaneously, we need to maintain two views of it, one from the previous
step and the in-progress view for calculating the next.
The adjacency rule is simple enough.

Since the states of the test data set were known, outputting that state
step by step is useful when debugging - as is limiting the number of steps
that are run (rather than accidentally letting the process continue forever)
due to an error in the rules.

For part 2, the adjacency rule has changed - and in such a way that I want
to represent the state differently.  The chairs don't move, so I can
build a representation that is a list of seats, and a reference to the
seat in each direction.

First I allocate an ID to each seat, to be the reference - then process
to find the seat in each direction.
Then finally I can use that representation to quickly generate the next
state without having to find a seat in each direction every iteration.

# Day 12

A bit of a throwback to day 8 today - the instruction set is
a simple set of machine code, but without jump instructions it is
even more trivial to implement.

The only trick for part 1 is to manage the correct facing.
Don't forget to use the absolute values of the resulting location to get the
Manhattan distance.

Part 2 gets a bit more tricky, needing to maintain a "waypoint" location
but that's not too hard.  The thing to remember is that rotation of a point
around the origin by 90 degrees swaps the x and y values over, and swaps the sign
of one of the results (swap sign of x to rotate right, y to rotate left)
No need to mess with trig functions

# Day 13

Sunday, a day of rest, so I guess that's why this puzzle is simpler.

Filter, map and sort should get us to an answer here.

But wait...  part 2 is much more complex, I have a feeling some participants
are going to get stuck here, especially with the comparative ease of the
first part.

We can reach the answer iteratively.
For the first two buses, we have to solve the problem so that
timestamp ```t``` is a multiple of the first id, and ```t+1``` is a multiple of the
second id - this yields an answer for us that feeds into the next step
the pattern will repeat every N (where N is the product of the current
answer and the current bus - it's possible that it could repeat sooner
depending on prime factors, but it did not in my dataset)

I spent a while trying to find an efficient way to calculate the next
answer, but this is not an easy problem - maybe a mathematician could
build a better solution.

Rinse and repeat this process until you reach the end of the list of buses.

The warning about the large timestamp suggests that we are going to need
to use some library or language feature that can cope with large numbers
Js has BigInt as a language feature that copes very well.

Also remember from an efficiency perspective, if there is a calculation
that can be done outside of a loop, do it outside the loop, BigInt
operations can be expensive.

A look at the stats confirms, this has about a third of participants
stuck after part 1 - so if you get through part 2, well done!

# Day 14

Last day of week two, and I've honestly had a lot of fun with this week's
problems.

The mask in todays problem is an interesting element, since it is not binary
it's ternary - each bit can be ```0```, ```1``` or ```X```.  I'm going to represent this as
two masks, one for setting bits, and one for clearing them.
So a mask of ```X1X0X``` would result in ```01000``` to set, and ```11101``` to clear - using
the expression ```(value or setmask) and clearmask``` to get the new value
The only challenge is parsing the mask to create the two mask values - since
we are going to overflow an int...  BigInt to the rescue (prefix binary with '0b'
to interpret as a binary string in the constructor)

One of the other tricky parts of the puzzle is that the address space is 36 bits
which means we need to ensure that we use a sparse array to manage it.

So part 2 modifies everything significantly, and touches a bit on quantum
computing - each address reference is a bit like addressing with qubits
I'm converting all the addresses to a 36 bit binary string to make this
process easier.

# Final words

I have really enjoyed this week - a lot of the problems had a good amount of difficulty
with complexity not only in the algorithm but on what was needed to store the state
for that algorithm.

I notice that unlike an advent calendar, this challenge has a "day 25" - so there's
a Christmas day problem too.  I'll post another week, and then the final few days
will have to wait until I get a moment after stuffing myself full of yummy food.
