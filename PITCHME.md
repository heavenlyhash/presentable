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

What "content addressable" did for version control,

"computation addressable" can do for builds.

---

To do this, we have to totally describe our computation.

---

Reproducible Materials +

Reproducible Action

=>

Reproducible Results <span style="color:grey;font-size:80%">(maybe!)</span>

---

Think of Repeatr like a lab notebook...

... but you write the procedures first, and then
an army of robots carries it out for you.

In a clean, sealed room.

And reports the results.

---

how

do

we

do

it

---

Eat the elephant one byte at a time...

---

## Reproducible Input Materials

Version control: for source, *and <span style="font-size:110%">b e <span style="font-size:110%">y o <span style="font-size:110%">n d !</span></span></span>*

---

Building software usually starts with a git checkout.

Building software usually starts with a git **hash**.

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

+++

`repeatr run` is a command that takes those inputs, and emits those results as json on stdout.

It's made to play nice.  (Pipe it into 'jq', etc...)

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
```

+++

`repeatr run` is a command that takes those inputs, and emits those results as json on stdout.

It's made to play nice.  (Pipe it into 'jq', etc...)

```
$ repeatr run the.formula
// ... logs on stderr ...
// exactly one json object on stdout:
{"exitcode": 0, "/just/the/tip":"3o4i0kmwrLFsRV0sLUzq3o3"}
```

---

## Result Gathering

<small>*If a pure function runs in a forest...*</small>

---

When you write a formula, you list paths you want to save results from.

```
"outputs": {
	"/just/the/tip": {type: "tar", warehouse: "file+ca://./wares/"}
},
```

---

When you run the formula, it emits results for each output requested:

```
{"exitcode": 0, "/just/the/tip":"3o4i0kmwrLFsRV0sLUzq3o3"}
```

Outputs are automatically hashed again -- easy to compare


---

If you make your formula a pure function, you're awesome

If you don't, you're... well, at least you're going to notice

---

Outputs are automatically hashed again...

... and you can use that for storage

Like `ls .git/objects/*`

---

Don't bother naming things.  Naming is hard

`diff` your outputs later

---

## All together now

---

**Formulas are complete descriptions.**

<small>what can we do with that?</small>

---

honestly, sheer convenience

---


**Formulas are complete descriptions.**

<small>what can we do with that?</small>

- Convenience: no toolchain questions; automatic toolchain fetch.
- <small>...*what else?*</small>

+++

"Deep Time Reproducibility"

No dependencies anything outside of localhost

No *logic* in fetching -- no "helpful" behavior with "interesting" side effects

+++

If you don't have any of the assets, that's trouble

But at least you **know**

---

**Formulas are complete descriptions.**

- Convenience: no toolchain questions; automatic toolchain fetch.
- Deep Time reproducibility

---

**Formulas are self-identifying.**

<small>what can we do with that?</small>

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

+++

```
(+ 2 2)  // 4
```
<br>
```
map.put("(+ 2 2)", 4)
```
<br>
```
(computePrimeFactors 2304920398409 239834985349875)
```

+++

Memoized functions are the FASTEST functions

```
// key := hash(inputs, script, save)
goFastFunction := new_memoizers(repeatrRun)
results := goFastFunction(inputs, script, save)
// next line is instantaneous
already := goFastFunction(inputs, script, save)
```

+++

Of course you may not want to *assume* your formula represents a pure function...

---

**Formulas are self-identifying.**

<small>what can we do with that?</small>

- memoize entire builds
- <small>...*what else?*</small>

---

We're here because we care about reproducible results...

Reproducible is a state of mind

*I* can't say something is reproducible, only *we* can say something is reproducible

---

Wouldn't it be nice if we had a global log of builds?

```
setup_key := hash(inputs, script, save)
myResult := repeatrRun(inputs, script, save)

otherResults := publicLog.fetch(setup_key)
foreach result in otherResults:
	assert(result == myResult)
```

With a natural primary key to query on?

---

**Formulas are self-identifying.**

<small>what can we do with that?</small>

- memoize entire builds
- find comparable results -- O(1) hashmap lookup

---

**Outputs are Inputs**

---

**Outputs are Inputs**

Saving outputs emits a hash...

... it's the same hash as inputs.

---

Feed it forward...

... or look backwards.

---

**Formulas are complete descriptions.**

- Deep Time reproducibility

**Formulas are self-identifying.**

- memoize entire builds
- find comparable results -- O(1) hashmap lookup
