Repeatr
=======

---

Repeatr is not a package manager.

Repeatr is not a build tool.

---

Repeatr is a "<span style="color:orange">computation addressable</span>" system.

Computation definition|representation|specification|...

Something like that.  We need to make up words for it because this is not a common concept!

---

You can use it to produce better build tools and package management.

---

Reproducible Environment +

Reproducible Materials +

=>

Reproducible Results <span style="color:grey;font-size:80%">(maybe!)</span>

---

Think of Repeatr like a lab notebook...

... but you write the procedures first, and then
an army of robots carries it out for you.

In a clean, sealed room.

And reports the results.

---

## Reproducible Input Materials

Version control: for source, *and <span style="font-size:110%">b e <span style="font-size:110%">y o <span style="font-size:110%">n d !</span></span></span>*

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
      hash: "aLMH4qK1EdlPDavdhErOs0BPxqO0i6lUaeRE4DuUmnNMx"
   "/app/go/":
      type: "tar"
      hash: "vbl0TwPjBrjoph65IaWxOy-Yl0MZXtXEDKcxodzY0_-in"
   "/task/repeatr/":
      type: "git"
      hash: "940ea614c3a3eeb410afd859b25ca6be648033b9"
```

---

*> > > &nbsp; Z o o m &nbsp; o u t &nbsp; > > >*

Your oldskoole CI system reports the git hash it built.

Upgrade time.

Let's build a CI system reports <span style="color:orange">*all*</span> the hashes it built from.

---

So we have reproducible environments.

Let's do something with them...

---

## Formulas

An indivisible, atomic unit of work.

---

*atom*: a hypothetical particle of matter so minute as to admit of no division.

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
(repeatrRun,
  /*input*/ [{"/path","hash-abcd"}, {"/path2","hash-qwer"}],
  /*script*/ ("/bin/bash", "-c", "..."),
  /*output*/ ["/savethis", "/and/this"],
) -> (
  /*result!*/ {"/savethis": "hash-3498"; "/and/this": "hash-7894"},
)
```

---

```bash
cat > the.formula <<EOF
{
    "inputs": {
        "/": {type: "tar", hash: "aLMH4qK1EdlPDavdhErOs0BPxqO0i6lUaeRE4DuUmnNMxhHtF56gkoeSulvwWNqT"},
        "/app/go/": {type: "tar", hash: "gi0Kpb-VH3TK0UBX6YmpuKsrMAUlxicPrY2YvXPo9sBQm_NsD_hKrn7pmc95zrmM"},
        "/task/repeatr/": {type: "git", hash: "8fb1a9ebd85eadacc861b0c149221af6808270d4"},
    },
    "action": {
        cwd: "/task/repeatr/"
        env: {"ENVVAR": "/v/a/l/u/e", "WOW": "yeah"},
        command: {"bash", "-c", "..."}
    },
    "outputs": {
        "/just/the/tip": {type: "tar", warehouse: "file+ca://./wares/"}
    },
}
EOF
repeatr run the.formula
```

```text
// ... logs on stderr ...
{"hai":"lo"}
```

---

*sidetrack*

Is this a lie?

Of course it is.

Kernel version, `readdir` syscall return list order, etc...

---

"We're all consenting adults here"

Yes, you *can* `cat /dev/random`

We agree that if you do that, you're Bad and should Feel Bad

<!-- Speaker Notes: talk about the network here!  That's as bad, or worse, that cat-random. -->
<!-- Potentially *both ways*.  You can put bananas into redis too... -->

---

**Formulas are memoizable.**

---

Memoization is the best

```
key := hash(inputs, script, save)
results := repeatrRun(inputs, script, save)

memo.put(key => results)
```

+++

<span style="color:grey">Wait, what is "memo...ization"</span>

1. Write a memo of your function and all its arguments.
2. Write down the result.
3. Don't run the function again.

---

`(+ 2 2)` `// 4`

`map.put("(+ 2 2)", 4)`

`(computePrimeFactors 2304920398409 239834985349875)`

---

Memoized functions are the FASTEST functions

```
// key := hash(inputs, script, save)
goFastFunction := new_memoizers(repeatrRun)
results := goFastFunction(inputs, script, save)
// next line is instantaneous
already := goFastFunction(inputs, script, save)
```

---

We're here because we care about reproducible results...

```
key := hash(inputs, script, save)
myResult := repeatrRun(inputs, script, save)

otherResults := publicLog.fetch(key)
foreach result in otherResults:
	assert(result == myResult)
```

What if we had a global log of builds?

With a natural primary key to query on?
