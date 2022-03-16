---
author: xkz
top: false
mathjax: false
img: /medias/featureimages/interpreter.jpg
date: 2022-03-02 20:13:39
title: CS61A Scheme Interpreter
tags: 
  - Python
  - Interpreter
categories: Python
summary: CS61A 课程中 HW08 和 Scheme Project 的总结
---

# Scheme Interpreter

## Introduction

在 CS61A 的 Project 4 要求自己实现一个 Scheme Interpreter. 实现的 Interactive Interpreter 主要工作流程为 REPL: Read-Eval-Print-Loop, 具体为:

1. Print a prompt
2. Read text input from the user
3. Parse the text input into an expression
4. Evaluate the expression
5. If any errors occur, report those errors, otherwise
6. Print the value of the expression and repeat

### Read

The interpreter takes the user input (a string) and passes it through a lexer and parser.

- The *lexer* turns the user input string into atomic pieces (tokens) that are like "words" of the implemented language. (词法分析)
- The *parser* takes the tokens and organizes them into data structures that the underlying language can understand. (有时也称 Syntactic analysis, 语法分析)

例如, 输入为

```scheme
(+ 1
   (- 23)
   (* 4 5.6))
```

在词法分析时, 一次读入一行, 会去掉不必要的空格, 并且进行数据类型的识别, 例如 `23` 会被识别为整数, 而 `a` 会被识别为 symbol, `5.6` 会作为完整的浮点数被读取, 处理后将得到:

```python
'(', '+', 1, '(', '-', 23, ')', '(', '*', 4, 5.6, ')', ')'
```

之后的词法分析会递归的进行处理, 进行括号匹配, 将之转化为 Python 的数据结构 (这里是一个类链表的 Pair 类型)

```python
Pair('+', Pair(1, Pair(Pair('-', Pair(23, nil)), nil), ...))
```

Pair 类型定义如下:

```python
class Pair:
    """A Pair has two instance attributes:
    first and second.
    For a Pair to be a well-formed list,
    second is either a well-formed list or nil.
    Some methods only apply to well-formed lists.
    """
    def __init__(self, first, second):
        self.first = first
        self.second = second
```

### Eval

Eval 是两个函数 `Eval` 和 `Apply` 的相互递归调用:

- `Eval` takes an expression and evaluates it according to the rules of the language. Evaluating a call expression involves calling `apply` to apply an evaluated operator to its evaluated operands.
- `Apply` takes an evaluated operator, i.e., a function, and applies it to the call expression's arguments. Apply may call `eval` to do more work in the body of the function, so `eval` and `apply` are *mutually recursive*.

需要 Evaluate 表达式时, 首先是调用 `Eval` 处理该表达式, 这时就要分别 Evaluate Operator 和 Operands, 然后再调用 `Apply`, 而 `Apply` 在调用时需要判断该表达式是否是 Builtin Procedure, 如果是, 调用对应的 Builtin Procedure, 如上例, 否则应该创造一个 Environment, 绑定参数和值, 然后继续调用 `Eval` 处理该表达式的主体部分, 最后递归的终点一定是 Builtin Procedure.

## Lab 11

为了有个适应的过程, Lab 11 让我们先做了一个小型的 PyCombinator Interpreter, 可以调用 `add`, `mul` 等 Builtin, 并且能够创建并调用 Lambda:

```scheme
> add(3, 4)
7
> mul(4, 5)
20
> sub(2, 3)
-1
> (lambda: 4)()
4
> (lambda x, y: add(y, x))(3, 5)
8
> (lambda x: lambda y: mul(x, y))(3)(4)
12
> (lambda f: f(0))(lambda x: pow(2, x))
1
```

一共包含 5 个 `.py` 文件,

+ `utils.py` 只包含一个 `comma_separated` 函数, 能够把传入的列表里的 String 组合成用逗号分隔的一整个 `String`.

+ `buffer.py` 包含自己写的一个 `Buffer` 类, 其实就是一个队列, 存储词法分析后的 token, 能够 peek (对应 `current()`) 和 `pop_first()`.

+ `repl.py` 就是进行 Read-Eval-Print-Loop

+ `expr.py` 包含两个基类 `Expr` 和 `Value`

    其中 `Expr` 的子类有 `Literal`, `Name`, `LambdaExpr`, `CallExpr`, 是调用 `reader.py` 中 `read` 函数后返回的结果. 每个子类都有自己的 `eval` 方法和 `__str__` 表示

    `Value` 子类则为 `Number`, `LambdaFunction` 和 `PrimitiveFunction`, 在 Evaluate `Expr` 后得到, 每个子类有自己的 `apply` 方法.

+ `reader.py` 中, `tokenize` 是 *Lexer*, `read_expr` 则是 *Parser*.

## Project 4: Scheme Interpreter

Here are all the files included in the archive:

- `scheme.py`: implements the REPL and a evaluator for Scheme expressions
- `scheme_reader.py`: implements the reader for Scheme input. The `Pair` class and `nil` definition can be found here.
- `scheme_tokens.py`: implements the tokenizer for Scheme input
- `scheme_builtins.py`: implements built-in Scheme procedures in Python
- `buffer.py`: implements the `Buffer` class, used in `scheme_reader.py`
- `ucb.py`: utility functions for use in 61A projects
- `questions.scm`: contains skeleton code for Phase III
- `tests.scm`: a collection of test cases written in Scheme
- `ok`: the autograder
- `tests`: a directory of tests used by `ok`
- `mytests.rst`: a file where you can add your own tests

  You will turn in the following files:

- `scheme_reader.py`
- `scheme.py`
- `questions.scm`
- `tests.scm`

### Problem 01

是 `scheme_read` 和 `read_tail` 的互相调用, `scheme_read` 可以读取一个完整的表达式, `read_tail` 读取一个完整的 `Pair`, 下面的描述对解题很有帮助:

- `scheme_read` removes enough tokens from `src` to form a single expression and returns that expression in the correct internal representation.
- `read_tail` expects to read the rest of a list or pair, assuming the open parenthesis of that list or pair has already been removed by `scheme_read`. It will read expressions (and thus remove tokens) until the matching closing parenthesis `)` is seen. This list of expressions is returned as a linked list of `Pair` instances.

```python
def scheme_read(src):
    """Read the next expression from SRC, a Buffer of tokens.

    >>> scheme_read(Buffer(tokenize_lines(['nil'])))
    nil
    >>> scheme_read(Buffer(tokenize_lines(['1'])))
    1
    >>> scheme_read(Buffer(tokenize_lines(['true'])))
    True
    >>> scheme_read(Buffer(tokenize_lines(['(+ 1 2)'])))
    Pair('+', Pair(1, Pair(2, nil)))
    """
    if src.current() is None:
        raise EOFError
    val = src.pop_first()
    if val == 'nil':
        return nil
    elif val == '(':
        return read_tail(src)
    elif val in quotes:
        return Pair(quotes[val], Pair(scheme_read(src), nil))
    # 注意之后还是继续调用 `scheme_read`, quote 只是加上一层括号而已
    # 例如 '(1 2) 转换成 (quote (1 2))
    elif val not in DELIMITERS:
        return val
    else:
        raise SyntaxError('unexpected token: {0}'.format(val))


def read_tail(src):
    """Return the remainder of a list in SRC, starting before an element or ).

    >>> read_tail(Buffer(tokenize_lines([')'])))
    nil
    >>> read_tail(Buffer(tokenize_lines(['2 3)'])))
    Pair(2, Pair(3, nil))
    """
    try:
        if src.current() is None:
            raise SyntaxError('unexpected end of file')
        elif src.current() == ')':
            src.pop_first()
            return nil
        else:
            return Pair(scheme_read(src), read_tail(src))  # 本题难点
    except EOFError:
        raise SyntaxError('unexpected end of file')
```

### Problem 03

要实现 Builtin Procedure 的 `apply` 方法, 这里为了使用 `fn(*lst)` 的语法, 需要先把传进来的 `Pair` 类型转成 Python List, 但是**不需要递归转换**.

```python
def apply(self, args, env):
        """Apply SELF to ARGS in ENV, where ARGS is a Scheme list (a Pair instance).

        >>> env = create_global_frame()
        >>> plus = env.bindings['+']
        >>> twos = Pair(2, Pair(2, nil))
        >>> plus.apply(twos, env)
        4
        """
        if not scheme_listp(args):
            raise SchemeError('arguments are not in a list: {0}'.format(args))
        python_args = []
        # Convert a Scheme list to a Python list
        while args:
            python_args.append(args.first)
            args = args.rest

        if self.use_env:  # 最后可以放字典参数, 作为 binding
            python_args.append(env)
        try:
            return self.fn(*python_args)
        except TypeError:
            raise SchemeError('Incorrect number of arguments to function call')
```

### Problem 04

To evaluate a call expression, we do the following:

1. Evaluate the operator (which should evaluate to an instance of `Procedure`)
2. Evaluate all of the operands (when there are multiple operands, we evaluate from left-to-right)
3. Apply the procedure on the evaluated operands, and return the result

```python
def scheme_eval(expr, env, _=None):  # Optional third argument is ignored
    """Evaluate Scheme expression EXPR in environment ENV.

    >>> expr = read_line('(+ 2 2)')
    >>> expr
    Pair('+', Pair(2, Pair(2, nil)))
    >>> scheme_eval(expr, create_global_frame())
    4
    """
    # Evaluate atoms
    if scheme_symbolp(expr):
        return env.lookup(expr)
    elif self_evaluating(expr):
        return expr

    # All non-atomic expressions are lists (combinations)
    if not scheme_listp(expr):
        raise SchemeError('malformed list: {0}'.format(repl_str(expr)))
    first, rest = expr.first, expr.rest

    if scheme_symbolp(first) and first in SPECIAL_FORMS:
        return SPECIAL_FORMS[first](rest, env)
    else:
        procedure = scheme_eval(first, env)
        validate_procedure(procedure)
        if isinstance(procedure, MacroProcedure):
            return scheme_eval(procedure.apply_macro(rest, env), env)  # apply_macro 会进行宏替换, 之后还需要 eval 一次
        return scheme_apply(procedure, rest.map(lambda exp: scheme_eval(exp, env)), env)
```

### Problem 17

典型的动态规划问题, 就看 `denoms` 里的第一个数, `total` 能否要得起, 如果要, 之后还有可能要, 如果不要, 就彻底丢弃, 那么这样一定能够将 `denoms` 和 `total` 往小了转化. 考虑递归基:

+ 如果 `total = 0`, 成功, 直接返回 `(())`, 在回溯时会通过定义的 helper procedure `cons-all` 往里塞东西
+ 如果 `denoms` 为空, 此时失败, 直接返回 `nil`

注意到我们并没有考虑 `total < 0` 时的递归基, 因为 `total` 要不起时就不会选择减小 `total`, 而是会选择迭代 `denoms`, 所以不可能被减到小于 0.

```lisp
(define (cons-all first rests)
  (map (lambda (x) (append (list first) x)) rests))

; ; List all ways to make change for TOTAL with DENOMS
(define (list-change total denoms)
  (cond
    ((zero? total)
     (list nil))
    ((null? denoms)
     nil)
    ((< total (car denoms))
     (list-change total (cdr denoms)))
    (else
     (append (cons-all (car denoms)
                       (list-change (- total (car denoms)) denoms))
             (list-change total (cdr denoms))))))
```

### Problem 18

`let` 可以被转化为对 `lambda` 的调用, 例如:

```lisp
(let ((a 1) (b 2)) (+ a b))
;; Is equivalent to:
((lambda (a b) (+ a b)) 1 2)
```

要求写一个 `let-to-lambda` 函数, 把 `let` 表达式转成 `lambda`

```lisp
scm> (let-to-lambda '(let ((a 1) (b 2)) (+ a b)))
((lambda (a b) (+ a b)) 1 2)
```

首先实现了 `zip` 函数:

```lisp
(define (zip pairs)
  (list (map (lambda (x) (car x)) pairs)
        (map (lambda (x) (cadr x)) pairs)))
```

思路就是把形如 `((1 2) (3 4) (5 6))` 的 `list` 通过 `map` 转成 `(1 3 5)` 和 `(2 4 6)`, 再拼起来.

```lisp
; ; Converts all let special forms in EXPR into equivalent forms using lambda
(define (let-to-lambda expr)
  (cond 
    ((atom? expr) ; 这个和下面的 quoted 是递归终点
     expr)
    ((quoted? expr) ; 因为这里只用转换表达式, 不用 evaluate, 所以 quoted 也返回自身
     ; 注意 quoted 不用递归转换
     expr)
    ((or (lambda? expr) (define? expr)) 
     ; 这里 form 和 params 都不用转换(有例子说 lambda parameters not affected), body 可能需要递归进行转换
     (let ((form (car expr))
           (params (cadr expr))
           (body (cddr expr)))
       (cons form (cons params (map let-to-lambda body)))))
    ((let? expr) ; form 换成 lambda, params 需要转换, body 递归转换, 之后还要调用这个 lambda
     ; 注意 zip 之前还要再递归转换, 因为参数里也可能有 let
     ; params 转换为 (car (zip(let-to-lambda values))), 调用时的参数为 (cadr (zip(let-to-lambda values)))
     (let ((values (cadr expr))
           (body (cddr expr)))
       (cons (cons 'lambda
             (cons (car (zip (let-to-lambda values)))
                             (map let-to-lambda body)))
                   (cadr (zip (let-to-lambda values))))))
    (else ; 函数体, 继续递归即可
     (map let-to-lambda expr))))
```

### Problem 19

要求自己在解释器中进行尾递归的优化, 通过一个 Higher-Order-Function 改进原本的 `scheme_eval`:

```python
def optimize_tail_calls(prior_eval_function):
    """Return a properly tail recursive version of an eval function."""

    def optimized_eval(expr, env, tail=False):
        """Evaluate Scheme expression EXPR in environment ENV. If TAIL,
        return a Thunk containing an expression for further evaluation.
        """
        if tail and not scheme_symbolp(expr) and not self_evaluating(expr):
            return Thunk(expr, env)

        result = Thunk(expr, env)
        while isinstance(result, Thunk):
            result = prior_eval_function(result.expr, result.env)
        return result

    return optimized_eval


scheme_eval = optimize_tail_calls(scheme_eval)
```

改进以后的函数有了第三个参数 `tail`, 标记着它是不是尾递归.

根据提示: 

> When `scheme_optimized_eval` receives a non-atomic expression in a `tail` context, then it returns an `Thunk` instance. Otherwise, it should repeatedly call `prior_eval_function` until the result is a value, rather than a `Thunk`.

不难补出完整的代码. 这里主要讲一下优化尾调用 (tail call) 的思路:

首先是 `Thunk` 类, 其实就是一个表达式和对应环境的绑定, 这样的绑定可以让新的表达式复用之前的环境.

优化 tail call, 就是要让 tail call 不额外增加一个 environment, 对应到代码中就是返回了一个 `Thunk`, 该 `Thunk` 绑定的表达式是 tail call, environment 则复用当前的 `env`. 这样创建新环境就变成了创建 `Thunk`, 但是 `Thunk` 用完之后就会被 GC 回收掉, 就可以实现 constant space 了.

之后在非 `Thunk` 时, 就用原本的函数来 evaluate 这个表达式, 注意到原本表达式可能又是 tail context 的, 返回的可能还是 `Thunk`, 因此需要用 while 循环保证最后返回的是一个值而不是 `Thunk`.

此外, 还需要修改对 `tail context` 的判断, 在该 Project 里只需要考虑

1. `eval_all` 时最后一个 expression
2. `if` special form
3. `and`, `or` special form
