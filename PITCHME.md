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

Version control: for source, *and b e y o n d !*

---

Building software usually starts with a git checkout.

Building software usually starts with *a git hash*.

---

Let's do that for everything!

[todo: "HASH ALL THE THINGS" image]

---

[todo: "HASH HASH HASH HASH" seizuregif]

---

Let's put together a filesystem like this:

```yaml
inputs:
    "/":
        type: "tar"
        hash: "aLMH4qK1EdlPDavdhErOs0BPxqO0i6lUaeRE4DuUmnNMxhHtF56gkoeSulvwWNqT"
    "/app/go/":
        type: "tar"
        hash: "vbl0TwPjBrjoph65IaWxOy-Yl0MZXtXEDKcxodzY0_-inUDq7rPVTEDvqugYpJAH"
    "/task/repeatr/":
        type: "git"
        hash: "940ea614c3a3eeb410afd859b25ca6be648033b9"
```

Huzzah -- Reproducible environment!

---

*>>> Zoom out >>>*

Your CI system reports the git hash it built from, right?

Hash that list of inputs.

Now your CI system can report *that*.

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
