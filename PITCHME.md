Repeatr
=======

---

Repeatr is not a package manager.

Repeatr is not a build tool.

---

Repeatr is a "computation addressable" system.

Computation definition|representation|specification|...

Something like that.  We need to make up words for it because this is not a common concept!

---

You can use it to produce better build tools and package management.

---

Think of Repeatr like a lab notebook...

... but you write the procedures first, and then
an army of robots carries it out for you.

In a clean, sealed room.

And reports the results.

---

Reproducible Environment +
Reproducible Materials +
=>
Reproducible Results (!maybe)

---

## Reproducible Input Materials

Version control: source, *and beyond!*



---

## Formulas

An indivisible, atomic unit of work.

---

The unit of description of a computation in Repeatr is called a Formula.

It's a free-standing description of:

- A list of input filesystem materials
- A command (or script) to run in a container, provisioned with those filesystems
- A list of output filesystems to store as a product

---

(FP trigger warning)

---

Formulas are like a Pure Function:

```golang
repeatrRun(
	input []{path,hash},
	script string,
	save []path,
) (
	result []{path,hash}
)
```

---

**Formulas are memoizable.**

```
key := hash(inputs, script, save)
results := repeatrRun(inputs, script, save)

memo[key] = results!
```
