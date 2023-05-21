---
title: "[golang 비동기 패턴] Pipeline 패턴"
date: 2023-05-20T14:04:43.026Z
categories:
  - golang
tags:
  - golang
math: false
---
```
package main

import "fmt"

func worker(in chan int, do func(int) int) chan int {
	out := make(chan int)
	go func() {
		defer close(out)
		for i := range in {
			out <- do(i)
		}
	}()
	return out
}

func run(in chan int) chan int {
	for i := 0; i < 5; i++ {
		in = worker(in, func(num int) int {
			fmt.Println("Data process", num)
			return num + 1
		})
	}

	return in
}

func main() {
	in := make(chan int)
	out := run(in)

	go func() {
		for o := range out {
			fmt.Println("Data out", o)
		}
	}()

	// for i := 0; i < 5; i++ {
	// 	fmt.Println("Data in", i)
	// 	in <- i
	// }

	// for {

	// }
}
```