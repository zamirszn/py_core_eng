
Each question is followed by its answer.

---

## Question 1: Trace and repair a function

**Easy | 6 minutes | 10 marks**

A ticket should cost 7 units. The developer expects three tickets to produce the integer 21.

```python
def ticket_total(price, quantity):
    total = price * quantity
    print(total)
amount = ticket_total("7", 3)
print(amount)
```

### Q1 Part A [4 marks]
Before running the code, write the exact two output lines and state the value and type of `amount`.

**Answer**

```
777
None
```

`amount` is `None`, and its type is `NoneType`.

### Q1 Part B [3 marks]
Explain why multiplication behaves this way and why `amount` does not contain the printed result.

**Answer**

`"7" * 3` is string repetition, not arithmetic, because `price` is the string `"7"`. That gives `"777"`, which `print(total)` displays. The function has no `return` statement, so Python returns `None` by default. The printed value is only sent to the screen and is never handed back to the caller.

### Q1 Part C [3 marks]
Rewrite the function and its call so the function returns an integer total and does not print internally. Print the returned result once outside the function. You may assume numeric integer inputs in the corrected version.

**Answer**

```python
def ticket_total(price, quantity):
    return price * quantity

amount = ticket_total(7, 3)
print(amount)   # 21
```

---

## Question 2: Find the missing passes

**Easy to moderate | 9 minutes | 15 marks**

Return every score greater than or equal to 50, in the original order. Inputs are lists of integers from 0 to 100. An empty list must return an empty list.

```python
def passing_scores(scores):
    passed = []
    for index in range(len(scores) - 1):
        if scores[index] > 50:
            passed.append(scores[index])
    return passed
print(passing_scores([49, 50, 80, 65]))
```

### Q2 Part A [5 marks]
Predict the current printed output. Identify both independent defects and explain which result each defect loses.

**Answer**

The current output is `[80]`. There are two independent defects:

1. `range(len(scores) - 1)` skips the last element, so `65` is lost.
2. `> 50` excludes the boundary, so `50` is lost.

### Q2 Part B [6 marks]
Correct the function without changing the input list.

**Answer**

```python
def passing_scores(scores):
    return [score for score in scores if score >= 50]
```

This builds a new list and leaves the input untouched. An empty list returns `[]`.

### Q2 Part C [4 marks]
Write three executable assertions: one for the pass boundary 50, one for a single passing score, and one for an empty list.

**Answer**

```python
assert passing_scores([49, 50, 51]) == [50, 51]   # boundary 50
assert passing_scores([75]) == [75]               # single passing score
assert passing_scores([]) == []                   # empty list
```

---

## Question 3: Protect the original profile

**Moderate | 11 minutes | 20 marks**

The function should return a new profile with an extra tag. The original profile and its tags must stay unchanged. The profile has only a string name and a list of string tags; both keys always exist.

```python
def add_tag(profile, tag):
    updated = profile.copy()
    updated["tags"].append(tag)
    return updated
original = {"name": "Ada", "tags": ["python"]}
changed = add_tag(original, "testing")
print(original["tags"])
print(changed is original)
print(changed["tags"] is original["tags"])
```

### Q3 Part A [7 marks]
Before running the code, predict all three output lines. Explain what `copy()` copies here and which object is still shared.

**Answer**

```
['python', 'testing']
False
True
```

`copy()` makes a shallow copy. It creates a new outer dictionary, so `changed is original` is `False`. The values are copied by reference, so the `"tags"` list is still the same object in both dictionaries. The `append` therefore changes the original, and `changed["tags"] is original["tags"]` is `True`.

### Q3 Part B [7 marks]
Repair `add_tag` so its returned dictionary and tags list are independent of the original. Do not change the public function signature.

**Answer**

```python
def add_tag(profile, tag):
    updated = profile.copy()
    updated["tags"] = list(profile["tags"]) + [tag]
    return updated
```

`copy.deepcopy(profile)` followed by an `append` would also work. The version above gives the returned dictionary a brand-new list.

### Q3 Part C [6 marks]
Write assertions showing that the original tags remain unchanged and the returned tags contain the new tag. Then append another tag to the returned list and assert that the original still has only its initial tag.

**Answer**

```python
original = {"name": "Ada", "tags": ["python"]}
changed = add_tag(original, "testing")

assert original["tags"] == ["python"]
assert changed["tags"] == ["python", "testing"]
assert changed is not original

changed["tags"].append("extra")
assert original["tags"] == ["python"]
```

---

## Question 4: Validate an import

**Challenging | 13 minutes | 25 marks**

You receive a list of strings representing whole-unit amounts. Return a dictionary with `total` and `rejected`. Use Python `int(raw)` conversion: surrounding whitespace is accepted. A converted amount of zero or more is valid. Negative amounts and strings that cannot be converted must each increase `rejected` by one. An empty list returns both values as zero. Do not change the input.

```python
def summarise_amounts(raw_values):
    total = 0
    for raw in raw_values:
        try:
            total += int(raw)
        except:
            pass
    return {"total": total, "rejected": 0}
```

Required example: `["10", " 5 ", "bad", "-3", "0", ""]` must return `{"total": 15, "rejected": 3}`. Inputs are always strings; no other type validation is required.

### Q4 Part A [6 marks]
Identify three defects or risks in the supplied function. Explain why a bare `except` can hide an unrelated failure.

**Answer**

Defects and risks in the supplied function:

1. Negative amounts are accepted and subtracted from `total`, but they should be rejected.
2. `rejected` is hard-coded to `0`, so it never counts anything.
3. Invalid strings such as `"bad"` and `""` are silently swallowed with `pass` instead of being counted.

A bare `except` catches every exception, including `KeyboardInterrupt`, `SystemExit`, `NameError` and `MemoryError`. A typo or unrelated failure inside the `try` would be silently treated as a bad amount, so the bug would be hidden.

### Q4 Part B [11 marks]
Rewrite the function to meet every rule. Catch only the expected conversion exception.

**Answer**

```python
def summarise_amounts(raw_values):
    total = 0
    rejected = 0
    for raw in raw_values:
        try:
            amount = int(raw)
        except ValueError:
            rejected += 1
            continue
        if amount < 0:
            rejected += 1
        else:
            total += amount
    return {"total": total, "rejected": rejected}
```

Check on the required example: `"10"` and `" 5 "` give a total of 15. `"bad"`, `"-3"` and `""` are rejected, which makes 3. `"0"` is valid and adds nothing.

### Q4 Part C [8 marks]
Write four executable assertions covering the required mixed example, empty input, all rejected input, and a valid zero. State why checking only `total` could miss a bug.

**Answer**

```python
assert summarise_amounts(["10", " 5 ", "bad", "-3", "0", ""]) == {"total": 15, "rejected": 3}
assert summarise_amounts([]) == {"total": 0, "rejected": 0}
assert summarise_amounts(["x", "-1", ""]) == {"total": 0, "rejected": 3}
assert summarise_amounts(["0"]) == {"total": 0, "rejected": 0}
```

Checking only `total` can miss a bug because different inputs can produce the same total. For example, `["0"]` and `["bad"]` both give a total of 0, but one has `rejected` 0 and the other has `rejected` 1. A broken rejection count would go unnoticed.

---

## Question 5: Make stock reservation reliable

**Hardest | 16 minutes | 30 marks**

`stock` maps item names to available quantities. `order` is a list of `(item, quantity)` pairs. Stock values are non-negative integers; order quantities are integers, not booleans. Keys are strings. These shapes and types are guaranteed.

```python
def reserve_stock(stock, order):
    remaining = stock.copy()
    for item, quantity in order:
        if quantity > stock[item]:
            raise ValueError("Insufficient stock")
        remaining[item] = stock[item] - quantity
    return remaining
```

**Required behaviour**

- Return a new dictionary containing every stock key with its remaining quantity. Never modify `stock` or `order`, including when a request fails.
- An item may occur more than once in `order`. Its combined requested quantity must be reserved. Never allow a negative remaining quantity.
- Raise `ValueError` for an unknown item, a quantity of zero or less, or insufficient stock. Error message wording is your choice. An empty order returns an equal but separate dictionary.
- You may use `try/except` with an assertion that fails if `ValueError` is not raised. Tests must call the function and check a result or failure, not just print it.

### Q5 Part A [6 marks]
With `stock = {"pen": 5}`, trace `order = [("pen", 3), ("pen", 3)]`. Explain why the supplied code incorrectly succeeds, and identify the other validation gaps.

**Answer**

Trace:

- Line 1: `3 > stock["pen"]` (5) is false, so `remaining["pen"] = 5 - 3 = 2`.
- Line 2: `3 > stock["pen"]` is still checked against the original 5, so it is false again, and `remaining["pen"] = 5 - 3 = 2`.

The function returns `{"pen": 2}`, but 6 units were requested and only 5 exist. The check uses the untouched `stock` instead of the running `remaining`, so repeated items are never combined.

Other validation gaps:

- An unknown item raises `KeyError` instead of `ValueError`.
- A quantity of zero or less is not rejected. A negative quantity would actually increase stock.

### Q5 Part B [14 marks]
Repair the function to meet all requirements. Use only in-memory Python; no database or concurrency implementation is needed.

**Answer**

```python
def reserve_stock(stock, order):
    remaining = dict(stock)              # local copy; stock is never touched
    for item, quantity in order:
        if item not in remaining:
            raise ValueError(f"Unknown item: {item}")
        if quantity <= 0:
            raise ValueError(f"Invalid quantity for {item}")
        if quantity > remaining[item]:   # check against running total
            raise ValueError(f"Insufficient stock for {item}")
        remaining[item] -= quantity
    return remaining
```

Each line is checked against `remaining`, which already reflects earlier lines, so repeated items are combined and the result can never go negative. An empty order returns an equal but separate dictionary.

### Q5 Part C [10 marks]
Write four tests: successful repeated items, repeated items exceeding stock, an unknown item, and zero quantity. In the overselling test, make at least one earlier line valid, then assert the original stock is unchanged after the exception. Explain why editing a local copy protects the caller when a later line fails.

**Answer**

```python
# 1. successful repeated items
stock = {"pen": 5, "ink": 2}
result = reserve_stock(stock, [("pen", 2), ("pen", 3)])
assert result == {"pen": 0, "ink": 2}
assert stock == {"pen": 5, "ink": 2}

# 2. repeated items exceeding stock (earlier line valid)
stock = {"pen": 5, "ink": 2}
try:
    reserve_stock(stock, [("ink", 1), ("pen", 3), ("pen", 3)])
    assert False, "ValueError not raised"
except ValueError:
    pass
assert stock == {"pen": 5, "ink": 2}     # unchanged after failure

# 3. unknown item
stock = {"pen": 5}
try:
    reserve_stock(stock, [("pen", 1), ("book", 1)])
    assert False, "ValueError not raised"
except ValueError:
    pass
assert stock == {"pen": 5}

# 4. zero quantity
stock = {"pen": 5}
try:
    reserve_stock(stock, [("pen", 0)])
    assert False, "ValueError not raised"
except ValueError:
    pass
assert stock == {"pen": 5}
```

Editing a local copy protects the caller because all changes happen on `remaining`, a separate dictionary. If a later line fails, the exception discards `remaining` along with the half-applied changes, and the caller's `stock` was never touched. The operation either succeeds completely and returns a new dictionary, or fails and leaves no side effects.
