# Operator Overload Semantics

## GetOperatorOverload(lho, operator, rho)

1. If Type(**lho**) is not Object, then
   a. return
2. If Type(**rho**) is not Object, then
   a. return
3. Let **lC** be Get(**lho**, 'constructor')
4. If IsConstructor(**lC**) is **_false_**, then
   a. return
5. let **rC** be Get(**rho**, 'constructor')
6. If IsConstructor(**rC**) is **_false_**, then
   a. return
7. If **lC** and **rC** are _not_ the same Object value, then
   a. return
8. Let **operatorMethod** be ? Get(**rho**, Symbol.operator(**operator**)
9. If IsCallable(**operatorMethod**) is **_false_**, then
   a. return
10. return ? Call(**operatorMethod**, **lC**, **lho**, **rho**)

# The Subtraction Operator ( - )

```
AdditiveExpression: AdditiveExpression - MultiplicativeExpression
```

1. Let **lref** be the result of evaluating **AdditiveExpression**.
2. Let **lval** be ? GetValue(**lref**).
3. Let **rref** be the result of evaluating **MultiplicativeExpression**.
4. Let **rval** be ? GetValue(**rref**).
5. Let **result** be GetOperatorOverload(**lval**, '-', **rval**)
6. If Type(**result**) is Empty, then
   a. Let lnum be ? ToNumber(lval).
   b. Let rnum be ? ToNumber(rval).
   c. Let **result** be the result of applying the subtraction operation to lnum and rnum. See the note below 12.8.5.
7. Return **result**

# The Addition Operator ( + )

```
AdditiveExpression: AdditiveExpression + MultiplicativeExpression
```

1. Let **lref** be the result of evaluating **AdditiveExpression**.
2. Let **lval** be ? GetValue(**lref**).
3. Let **rref** be the result of evaluating **MultiplicativeExpression**.
4. Let **rval** be ? GetValue(**rref**).
5. Let **result** be GetOperatorOverload(**lval**, '+', **rval**)
6. If Type(**result**) is Empty, then
   a. Let lnum be ? ToNumber(lval).
   b. Let rnum be ? ToNumber(rval).
   c. Let **result** be the result of applying the addition operation to lnum and rnum. See the note below 12.8.5.
7. Return **result**



