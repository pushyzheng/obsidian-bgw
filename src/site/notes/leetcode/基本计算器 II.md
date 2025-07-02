---
{"dg-publish":true,"dg-permalink":"基本计算器 II","permalink":"/基本计算器 II/","title":"基本计算器 II","tags":["栈","数学"]}
---


#算法 

由于 `*` 和 `/` 的优先级比较高，所以需要通过一个栈来实现：
1. `+` 或者 `-` 号：直接入栈
2. `*` 或者 `/` 号：从栈中出栈后计算，再重新入栈

```java
public int calculate(String s) {
    Deque<Integer> stack = new LinkedList<>();
    int n = s.length();
    int num = 0;
    char pre = '+';

    for (int i = 0; i < n; i++) {
        char ch = s.charAt(i);
		// 由于数字可能是两位数，所以需要进行计算
        if (Character.isDigit(ch)) {
            num = num * 10 + ch - '0';
        }
        if (!Character.isDigit(ch) && ch != ' ' || i == n - 1) {
            if (pre == '+') {
                stack.push(num);
            } else if (pre == '-') {
                stack.push(-num);
            } else if (pre == '*') {
                stack.push(stack.pop() * num);
            } else {
                stack.push(stack.pop() / num);
            }
            pre = ch;
            num = 0;
        }
    }
    int res = 0;
    while(!stack.isEmpty()) {
        res += stack.pop();
    }
    return res;
}
```