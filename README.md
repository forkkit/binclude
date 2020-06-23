# binclude

[![GoDoc](https://img.shields.io/badge/go.dev-reference-007d9c?logo=go&logoColor=white&style=flat-square)](https://pkg.go.dev/github.com/lu4p/binclude)
[![Test](https://github.com/lu4p/binclude/workflows/Test/badge.svg)](https://github.com/lu4p/binclude/actions?query=workflow%3ATest)
[![Go Report Card](https://goreportcard.com/badge/github.com/lu4p/binclude)](https://goreportcard.com/report/github.com/lu4p/binclude)
[![codecov](https://codecov.io/gh/lu4p/binclude/branch/master/graph/badge.svg)](https://codecov.io/gh/lu4p/binclude)

binclude is a tool for including static files into Go binaries.
- focuses on ease of use
- the bincluded files add no more than the filesize to the binary
- uses ast for typesafe parsing and code generation
- each package can have its own `binclude.FileSystem`
- `binclude.FileSystem` implements the `http.FileSystem` interface
- `ioutil` like functions `FileSystem.ReadFile`, `FileSystem.ReadDir`
- include all files/ directories under a given path by calling `binclude.Include("./path")`
- high test coverage
- supports execution of executables directly from a `binclude.FileSystem` (os/exec wrapper)


## Install
```
go get -u github.com/lu4p/binclude/cmd/binclude
```
## Usage
```go
package main

//go:generate binclude

import (
	"io/ioutil"
	"log"

	"github.com/lu4p/binclude"
)

func main() {
	binclude.Include("./assets")
	binclude.Include("file.txt")

	f, err := BinFS.Open("file.txt")
	if err != nil {
		log.Fatalln(err)
	}

	out, err := ioutil.ReadAll(f)
	if err != nil {
		log.Fatalln(err)
	}

	log.Println(string(out))

	infos, err := BinFS.ReadDir("./assets")
	if err != nil {
		log.Fatalln(err)
	}

	for _, info := range infos {
		log.Println(info.Name())
	}
}

```
To build use:
```
go generate
go build
```
## Compression
Binclude does not do any compression on its own.

This is intentional for simplicity, you can however reduce the final binary size by building without debug info (`go build -ldflags "-s -w"`) and compressing the the resulting binary with [upx](https://upx.github.io/) (`upx binname`).
