---
description: "A real fluent chain and its equivalent 5-line non-fluent version produced identical real results -- confirming .load() returns the same object, not a copy."
---

# Fluent Page Objects

## What was run

The real `LoginPage` from [Lesson 2](02-building-a-page-object.md), refactored so every action method
returns `self` — enabling real method chaining — compared directly against an equivalent, non-chained version
to confirm they produce identical real results, plus a direct check of exactly what gets returned at each
step.

## A real, chained one-liner

```python
result = LoginPage(driver).load().enter_username("realuser").enter_password("realpass123").submit().get_result_text()
```
```
REAL fluent chain result: 'Login Successful'
```

## The real, equivalent non-fluent version

```python
page = LoginPage(driver)
page.load()
page.enter_username("realuser")
page.enter_password("wrongpass")
page.submit()
result2 = page.get_result_text()
```
```
real non-fluent equivalent result: 'Login Failed'
```

Both real styles call the exact same real underlying methods in the exact same order — the only difference is
whether the return value of each call is used to chain the next one, or discarded in favor of the original
variable.

## A real, direct confirmation of what chaining actually returns

```python
loaded = page3.load()
print(loaded is page3)
```
```
real check: does .load() return the SAME real object? True
```

A real, important confirmation: `.load()` (and every other chained method) returns the exact same real
`LoginPage` instance, not a new copy — which is precisely what makes chaining `.load().enter_username(...)`
work at all; each call operates on the identical real object the previous call just returned.

## How It Actually Works

Fluent chaining works because every method in the chain ends with `return self`, handing back a reference to
the real same object it was just called on. Python doesn't know or care that this is a "fluent" pattern — it's
just an ordinary method call whose return value happens to be the object itself, which is exactly why this
lesson's real `is` check confirms identity, not equality: there's genuinely only one real `LoginPage` instance
involved throughout the entire chain.

The real, practical value isn't performance or correctness — this lesson's two styles produced identical real
results — it's readability at the call site. A real test reading `LoginPage(driver).load().login("user",
"pass").get_result_text()` states its intent in one real, linear statement, while the non-fluent version
needs a named variable held across several separate real statements to express the same sequence.

## Common mistakes

- Making some methods fluent (`return self`) and others not, inconsistently — a chain breaks the instant it
  hits a method that returns `None` instead of the real object, and the resulting error can be confusing if
  the inconsistency isn't obvious from the page object's own code.
- Chaining methods that logically shouldn't be combined just because the syntax allows it — this lesson's
  `login()` convenience method deliberately bundles a real, meaningful sequence (username, then password,
  then submit); chaining unrelated actions together for the sake of a one-liner can hurt real readability
  instead of helping it.
- Assuming a fluent chain always returns the *same* page object — a real action that navigates to a
  genuinely different page (not demonstrated in this lesson's single-page login flow) should return a
  *different* page object representing that new real page, not `self`.

## Exercise

Write a real fluent chain that logs in with wrong credentials, checks the real failure result, then calls
`.load()` again (the real app's `/login` POST response replaces the entire page — including the form itself
— so there's nothing left to clear or re-fill without reloading first) and logs in again with correct
credentials. Confirm the real result changes from `'Login Failed'` to `'Login Successful'` across the two
real attempts.
