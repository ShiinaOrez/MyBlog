## Description

Implement a stack with following functions:

+ push(val) push val into the stack
+ pop() pop the top element and return it
+ min() return the smallest number in the stack

All above should be in O(1) cost.

## Solution

这个题的基本思想是同步记录栈内元素的最小值, 因此建立两个栈, 第一个栈是具有正常pop, push操作的栈, 第二个栈内的元素则是记录当时栈内高度对应第一个栈的栈内最小元素.

假设第一个栈为`normal-stack`, 第二个栈为`min-stack`

```
func push(number):
    normal-stack.push(number)
    if number < min-stack.top():
        min-stack.push(number)
    else:
        min-stack.push(min-stack.top())

func pop() int:
    min-stack.pop()
    return normal-stack.pop()

func min() int:
    return min-stack.top()
```
