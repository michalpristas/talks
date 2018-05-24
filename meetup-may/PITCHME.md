# Do you trust your compiler?
---

## About Me

- Microsoft Developer
- Using Go since 1.2 (2013)
- Twitter: @michalpristas
- Github: michalpristas

---

![dog](assets/image/dog.png)

---

## How we solved dependencies?

- Makefiles, go get |
- 1.4 gb |
- 1.5 go vendor experiment, godep |
- 1.6 vendor  |
- 1.7 glide 0.9 |
- 1.9 dep |

---

## 1.10 

### vgo

Documentation [https://research.swtch.com/vgo](https://research.swtch.com/vgo)

Code [https://github.com/golang/vgo](https://github.com/golang/vgo)

--- 

## vgo flow - step 1

Import

```go
package p 

import "github.com/michalpristas/foo"
import "github.com/michalpristas/bar/v2"
```

---

## vgo flow - step 2

Version selection

```go
module "my/thing"
require "github.com/michalpristas/foo" v1.0.2
require "github.com/michalpristas/bar" v2.3.4
```
@[1,3](go.mod file)

---

## vgo flow - step 3

- vgo fetches sources from github 
- and builds

That's it!

---

## What is solved so far?

- Minimal version selection |
- Import compatibility |
- Semantic versioning |
- Simple API updates |
- more iteresting things like |
---

## Verifiable builds 

```bash
$ goversion -mh ./hello
hello go1.10
	path  github.com/you/hello
	mod   github.com/you/hello  (devel)
	dep   golang.org/x/text     v0.0.0-20170915032832-14c0d48ead0c  h1:qgOY6WgZOaTkIIMiVjBQcw93ERBE4m30iBm00nkL0i8=
	dep   rsc.io/quote          v1.5.2                              h1:w5fcysjrx7yqtD/aO+QwRjYZOKnaM9Uh2b40tElTs3Y=
	dep   rsc.io/sampler        v1.3.1                              h1:F0c3J2nQCdk9ODsNhU3sElnvPIxM/xV1c/qZuAeZmac=
```

---

## Verified builds 

```bash
$ echo >go.modverify
$ vgo build
$ tcat go.modverify  # go get rsc.io/tcat, or use cat
golang.org/x/text  v0.0.0-20170915032832-14c0d48ead0c  h1:qgOY6WgZOaTkIIMiVjBQcw93ERBE4m30iBm00nkL0i8=
rsc.io/quote       v1.5.2                              h1:w5fcysjrx7yqtD/aO+QwRjYZOKnaM9Uh2b40tElTs3Y=
rsc.io/sampler     v1.3.0                              h1:7uVkIFmeBqHfdjD+gZwtXXI+RODJ2Wc4O7MPEh/QiW4=
```

---

## Verified builds - mismatch

```bash
$ vgo build
vgo: verifying rsc.io/quote v1.5.2: module hash mismatch
	downloaded:   h1:w5fcysjrx7yqtD/aO+QwRjYZOKnaM9Uh2b40tElTs3Y=
	go.modverify: h1:v5fcysjrx7yqtD/aO+QwRjYZOKnaM9Uh2b40tElTs3Y=
```

---

## Reproducible builds

>At the very least, we want to make sure that when you build my program, the build system decides to use the same versions of the code. Minimal version selection delivers this property by default.

---

## Reproducible builds

- Me: using package foo/bar v1.0 |
- Me: vgo get v1.0 |
- Me happy, build works |
- Pete: oh I forgot to add this |
- Pete: force push tag to keep v1.0 |
- My CI: vgo get v1.0  |

Is it really the same package?

---

## Proxy support

```bash
$ GOPROXY=https://gomods.io
```

---

## How flow is changed?
- Step 1: import |
- Step 2: detect |
- Step 3: vgo get from proxy |
- Step 4: 
  + Proxy responds with 404: go get VCS
  + Proxy serves package: use package, break;

---

## What is our goal, finally.

- Athens! |
- Fixed set of official proxies |
- Hosting SAME bits for module versions |
- Started At Microsoft |
- In cooperation with the Go team |
- Open Source: https://github.com/gomods/athens |

---

## What is Athens

The Athens project will build 
- a network of official proxy servers.
- an official central repository that is geographically distributed 

---

## Athens 

- Proxy server for vgo modules,
- Cache modules locally,
- Implements the download protocol,

---

## Olympus 

- The source of truth 
- Global registry

---

## How Athens are up to date?

- Reporting cache misses to Olympus |
- Pulling event log from Olympus |
- Event log is append only |
- Proxy has a pointer to a latest sync

---

## What about Olympus

- Each Olympus knows about neighbours |
- Unreliable push synchronization |
- Pull synchronization between instances 

---

## Private proxies?

- No problem!
- Do I need to report private cache misses?

---

## Sounds like a lot of data, who pays for this?

- We do!!

** We = TBH Foundation

*** to be formed

---

## We love contributions

https://github.com/gomods/athens

---