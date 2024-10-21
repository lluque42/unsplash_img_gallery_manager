# Unsplash gallery manager (A Galant meta pilot fullstack project)

Luis Luque (lluque) - 2024

Message to other 42 students: DO NOT COPY, get inspired!

TODO FROM HERE

## Introduction

From project's subject:
> You have 2 stacks named a and b.
> * At the beginning:
>    * The stack a contains a random amount of negative and/or positive
> numbers which cannot be duplicated.
>    * The stack b is empty.
> * The goal is to sort in ascending order numbers into stack a. To do so
> you have the following operations at your disposal:
>    * **sa (swap a)**: Swap the first 2 elements at the top of stack a.
> Do nothing if there is only one or no elements.
>    * **sb (swap b)**: Swap the first 2 elements at the top of stack b.
> Do nothing if there is only one or no elements.
> ss : sa and sb at the same time.
>    * **pa (push a)**: Take the first element at the top of b and put it
> at the top of a. Do nothing if b is empty.
>    * **pb (push b)**: Take the first element at the top of a and put it
> at the top of b. Do nothing if a is empty.
>    * **ra (rotate a)**: Shift up all elements of stack a by 1.
> The first element becomes the last one.
>    * **rb (rotate b)**: Shift up all elements of stack b by 1.
> The first element becomes the last one.
>    * **rr**: ra and rb at the same time.
>    * **rra (reverse rotate a)**: Shift down all elements of stack a by 1.
> The last element becomes the first one.
>    * **rrb (reverse rotate b)**: Shift down all elements of stack b by 1.
> The last element becomes the first one.
>    * **rrr**: rra and rrb at the same time..

## Directory tree

    ./  
    ├── .gitignore				(to prevent undesired files to be stagged)  
    ├── Doxyfile				(doxygen config file for doc generation)  
    ├── Makefile				(use 'make help' for instructions)  
    ├── README.md				(brief documentation)  
    ├── doc/					(documentation extracted from comments in .h)  
    │   ├── html/				(html documentation)  
    │   │   ├── ...  
    │   │   ├── index.html		(entry point for html documentation)  
    │   │   ├── ...  
    │   └── man/				(man pages documentation)  
    │       └── man3/  
    │           └── ...			(example of use: 'man -l ft_string.h.3')  
    ├── include/				(directory for public header files (.h))  
    │   ├── ...  
    │   └── ...  
    ├── src/					(dir for source code and private header files)  
    │   ├── ...  
    │   │   ├── ...  				
    │   │   └── ...  
    │   └── ...					(may be structured in several directories)  
    │       ├── ...  
    │       └── ...  
    ├── lib/					(external libraries)  
    │   ├── ...  
    │   └── libft				(each library in its own directory)  
    ├── bin/					(directory for project's binaries and tester)  
    │   ├── ...  
    │   └── ...					(may be structured in several directories)  
    ├── obj/					(dir for source code and private header files)  
    │   ├── ...  
    │   │   ├── ...  
    │   │   └── ...  
    │   └── ...					(structured as in ./src)  
    │       ├── ...  
    │       └── ...  
    └── test/					(test program src code)  
        ├── ...  
        ├── tester.c  
        └── ...  

## Compilation instructions

Use ‘make help’ for instructions.

## Data structures
The main struct type of the program, where both stacks a and b are referenced
as doubly linked circular lists.
```
typedef struct s_ps_stacks
{
    t_dlclst    *a;
    t_dlclst    *b;
    int         left_to_pre_sort;
}   t_ps_stacks;
```
The element struct type, where each of the numbers is storaged, along to its
sorting metadata. The element type is that which is linked to the *content*
member of the dlclst node.
```
typedef struct s_element
{
    int     value;
    int     pos_when_sorted;
    int     pos_in_stack;
    int     cost_a;
    int     cost_b;
}   t_element;
```
## Layered implementation approach
Very early in the design stage it became obvious that defining a few
abstraction levels was the best approach to implement this project. From the
lowest to the highest level, each level of abstraction builts on its
immediate lower level. The main purpose of this was to keep the code legible
while compartmentalizing: when working on (or at) a level, the details of
the lower levels are ignore and only dealt with, indirectly, through the
immediately lower level.
In a nutshell, from lowest to highest:
* A collection of functions and structs data types to implement a doubly
linked circular list (dlclst) was added to libft for this project. The choice
for doubly linking responds to facilitate the swaping of nodes, while the
circularity is ideal for the rotation of the nodes. The following image
depicts an example dlclst list showing the links and directions:
```
                                                                            .
                                                                            .
                                 _______________________________________
   ^          prev (of my_list) |          _________________________    | 
   ^                            v         | next (of node5)         |   | 
   ^                               node5                            |   | 
   ^            prev (of node5) |         ^                         |   | 
next dir                        v         | next (of node4)         |   | 
                                   node4                            |   | 
                prev (of node4) |         ^                         |   | 
                                v         | next (of node3)         |   | 
                                   node3                            |   | 
                prev (of node3) |         ^                         |   | 
                                v         | next (of node2)         |   | 
                                   node2                            |   | 
                prev (of node2) |         ^                         |   | 
prev dir                        v         | next (of node my_list)  |   | 
   v        t_dlclst *my_list     my_list                           |   | 
   v                            |         ^ next (of node5)         |   | 
   v                            |         |_________________________|   | 
   v                            |_______________________________________| 
                                   prev (of my_list)
                                                                            .
                                                                            .
```
* A collection of functions and data structures was developed using dlclst
to implement the set of two stacks and every push-swap language instruction:
pa(), pb(), sa(), sb(), ss(), ra(), rra(), rb(), rrb(), rr(), and rrr(). Also
several functions were added to support peeking of elements, etc. The
following image depicts an example of a stack showing the effects of every
push-swap language instruction:
```
         ^                                                                  .
         ^                                                                  .
         ^             stack A                  stack B
         ^               1  >>>>>>> pa() >>>>>>>             A sa() swaps the
    ra(), rr()           8    (from top to top)                top and the
     direction           3  (pb() does the same,              second-to-top,
    (cost > 0)           7    mutatis mutandi)                 that is, from:
(dlclst "prev" dir)      2                                          1
                         6                                          8
   rra(), rrr()          4                                         ...
     direction           0                                         to:
    (cost < 0)           5                                          8
(dlclst "next" dir) 9                                               1
         v                                                         ...
         v                                                   sb() and ss()
         v                                                   do the same,
         v                                                  mutatis mutandi
                                                                            .
                                                                            .
```

For clarity sake, let's put all the former information in an example.

This example will aid to clarify all the conventions in this implementation
as well as helping while navigating (or, oh my God of course, while writing)
the code.

When invoking the program as:
```
                                                                            .
bin/mandatory/push_swap 1 8 3 7 2 6 4 0 5 9
                                                                            .
```
The arguments are read from left to right. Then, each argument, after
validation, is bottom_pusha()'ed into stack A. The bottom_pusha() function
ultimately uses ft_dlclst_insback() to place the node with the associated
integer at the end of the dlclst that represents the stack A (the integer ends
at the bottom of stack A). In this way, the first integer in the arguments
lands on the top of stack A, while the last integer in the arguments lands on
the bottom of stack A.

The following image shows the initial state of stack A with an example
invocation of the program (notice de dlclst "next" and "prev" directions):
```
                                                                            .
                                                                            .
                         Stack X
                                       (get_value(ft_dlclst_last(ps->X))
                  top of X    1 (0) < first node in the respective dlclst
  dlclst          top_1 of X  8 (1)
 next dir                     3 (2)
   v        ^                 7 (3)
   v        ^                 2 (4)
   v        ^                 6 (5)
   v        ^                 4 (6)
         dlclst               0 (7)
        prev dir  bot_1 of X  5 (8)
                  bottom of X 9 (9) < last node in the respective dlclst 
                                           (get_value(ps->X))
                                                                            .
NOTE: Values in parenthesis are the respective element->pos_in_stack.
                                                                            .
                                                                            .
```

The top of each stack is the last node in the dlclst that implements it:
```
                                                                            .
                                                                            .
$ bin/mandatory/push_swap 1 8 3 7 2 6 4 0 5 9
                                                                            .
         stack A                                         stack B
                     (get_value(ft_dlclst_last(ps->a))
top of A    1 (0) < first node in the respective dlclst >>>
top_1 of A  8 (1)
            3 (2)
            7 (3)
            2 (4)
            6 (5)
            4 (6)
            0 (7)
bot_1 of A  5 (8)
bottom of A 9 (9) < last node in the respective dlclst >>> 
                         (get_value(ps->a))
                                                                            .
NOTE: Values in parenthesis are the respective element->pos_in_stack.
                                                                            .
                                                                            .
```
A pushing instruction moves one of the stack's top element to the other stack
landing at the top position.

From the state of the stacks depicted in the image above, the following image
shows the resulting state after performing five pushings to A (i.e. 5 x
pa()): 
```
                                                                            .
                                                                            .
         stack A                                         stack B
                     (get_value(ft_dlclst_last(ps->X))
top of A    6 (0) < first node in the respective dlclst > (0) 2 top of A
top_1 of A  4 (1)                                         (1) 7 top_1 of A
            0 (2)                                         (2) 3
bot_1 of A  5 (3)                                         (3) 8 bot_1 of A
bottom of A 9 (4) <  last node in the respective dlclst > (4) 1 bottom of A
                         (get_value(ps->X))
                                                                            .
NOTE: Values in parenthesis are the respective element->pos_in_stack.
                                                                            .
                                                                            .
```
Eventually, the final state after sorting is shown in the following image:
```
                                                                            .
                                                                            .
         stack A                                         stack B
                     (get_value(ft_dlclst_last(ps->a))
top of A    0 (0) < first node in the respective dlclst >>>
top_1 of A  1 (1)
            2 (2)
            3 (3)
            4 (4)
            5 (5)
            6 (6)
            7 (7)
bot_1 of A  8 (8)
bottom of A 9 (9) < last node in the respective dlclst >>> 
                         (get_value(ps->a))
                                                                            .
NOTE: Values in parenthesis are the respective element->pos_in_stack.
                                                                            .
                                                                            .
```
## The sorting algorithm
### Introduction
A careful reading of the subject makes it obvious that the only optimization
is regarding the number of ps_lang instructions that must be used to sort
the numbers. That is, whatever it takes to achieve that is not only
legitimate but neccesary.

In this line, the approach taken is to "cheat" by first ordering the list
using some classical-I-don't-care-how-efficient algorithm and, using its
results, statistical analysis, etc. find out the minimum number of ps_lang
instructions to get to the same sorted stack A.

The following stages of the sorting process can be explicitly followed in
ps_sort() function:
* Sorting preparations
* Pre sorting
* Cost-based pushing
* Rotate stack A until sorted


####Sorting preparations

Once every number passed is placed in the stack A, the values in this stack
are copied to an array of integer to be sorted by some classical sorting
algorithm (bubble sorting in this case, this could be improved in the future).
The purpose of this step is to attach (as meta-data) to every element in stack
A the position it will have once sorted. This pos_when_sorted is the key to
the next stage of the sorting process.

####Pre sorting

**Pre sorting** is the process to smartly push to stack B every element in
stack A but three. The elements in stack A are processed block-by-block and
the criteria for pushing to stack B depends on the avg_pos_when_sorted of
the elements still in stack A. Both the block size (half of the remaining
elements in stack A to be pushed, plus one) and the avg_pos_when_sorted is
re-calculated at the begining of every block processing.

For each block of elements to be pushed from A to B, the top A element, the
next-to-top A element, and the bottom A element (may be in a future version
also the next_to_bottom A element) are examined. The first of the later which
its pos_when_sorted is smaller than the current avg_pos_when_sorted is pushed
to B. If none passes this test, a ra() is performed to try again. This goes
on until the number of elements pushed to B equals the target block size,
then, the next block is processed and so on, until only three elementes are
left in stack A. When this is achieved, the three remaining elements in A are
sorted. These will serve as the first references for the next stage, where
the elements in stack B are pushed back to A (after lots of smart rotations)
according to movement costs analysis.

####Cost-based pushing

**Cost-based pushing** is the stage of the sorting process where stack B is
emptied by using a sequence of ps_lang rotation instructions (which may
affect both stacks) ending, of course, with a final pa().

Cost-based pushing is a loop that only breaks when stack B is empty, in this
loop the following is performed:
* Set positions in stack to every element in both stack A and B. This is
the relative position each element has in the stack it is in
(set_pos_in_stack()).
* Set costs values for EACH element in stack B (set_costs_values()).
    * There are two components for cost value: cost_a and cost_b. The reason
    there are two is because, for the current element in stack B being
    evaluated, the costs in rotational movements must be calculated for
    both stacks:
        * From the stack B perspective, how many rotations IN THE BEST
        DIRECTION are needed to get the current element in stack B being
        evaluated to the top position so it can be pushable to stack A.
        * From the stack A perspective, how many rotations IN THE BEST
        DIRECTION are needed to put it in a state where pushing the top
        element in stack B (the one that is currently being analyzed) makes
        it land in the best position sorting-wise, that is, maintaining a
        circularly sorted stack A.
    * For both cost_a and cost_b the following convention is adopted: a
    positive value means that rotations are performed in the ra() direction
    (up: top goes to bottom). This is equivalent to the dlclst "prev"
    direction. A negative cost_a or cost_b value means that rotations
    are performed in the rra() direction (down: bottom goes to top). This is
    equivalent to dlcslt "next" direction.
    * The total_cost for the current element in stack B being analyzed
    depends on the absolute values of cost_a and cost_b and their signs as
    follows:
        * If cost_a and cost_b have the **same sign**, this implies that
        **simultaneous rotations on both stacks MAY** be performed. In this
        case, the total_cost is the sum of:
            * The absolute value of the difference of cost_a and cost_b. This
            is the number of the common rotations in the same direction for
            both stacks, plus
            * The remaining number of rotations of just one of the stacks,
            that is, the absolute value of the biggest between cost_a and
            cost_b, minus the former value (the common rotations).
        * If cost_a and cost_b have **different signs**, this implies that
        **simultaneous rotations on both stacks MAY NOT** be performed. In
        this case, the total_cost is the sum of the absolute values of cost_a
        and cost_b, since the program must rotate in different directions
        each stack to get the stack A in the sweet spot and the current
        element in stack B being analyzed at the top of stack B to make it
        pushable to A.
* Find the lowest total cost element in stack B
(get_lowest_cost_element_pos()).
* Rotate before pa: This is the execution of the sequence of rotations needed
before being able to perform the pa instruction to push to stack A the lowest
cost element in B (rotate_before_pa()). This rotation instructions aim to:
    * Place the desired (lowest cost) element in stack B in the top of the
    stack.
    * Reorder stack A so that when pa'ing the desired element in stack B
    lands in the desired position in stack A (stack A stays circularly
    sorted).
* Finally, a pa() to push the right element in stack B to the right position
in stack A.

####Rotate stack A until sorted

As the result of Cost-based pushing, Stack B is empty and the elements in
Stack A are ONLY CIRCULARLY sorted. In this final stage the stack A is
rotated in the best direction to get it LINEARLY sorted.
