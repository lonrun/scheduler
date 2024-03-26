# Scheduler

[![GoDoc](https://godoc.org/github.com/lonrun/scheduler?status.svg)](https://godoc.org/github.com/lonrun/scheduler)
[![Build Status](https://travis-ci.org/lonrun/scheduler.svg?branch=master)](https://travis-ci.org/lonrun/scheduler)
[![Coverage Status](https://coveralls.io/repos/lonrun/scheduler/badge.svg?branch=master)](https://coveralls.io/r/lonrun/scheduler?branch=master)

Job scheduling made simple. The Scheduler package provides a fluent syntax for scheduling recurrent jobs. It's inspired by and based on **[schedule](https://github.com/carlescere/scheduler)**.

## Getting Started
lonrun
To use Scheduler, import the package and start scheduling jobs:

```go
package main

import (
	"fmt"
	"runtime"
	"time"
	"github.com/lonrun/scheduler"
)

func main() {
	// Define jobs
	job1 := func() {
		fmt.Println("Job1: Time's up! @", time.Now().UTC())
	}
	job2 := func(params ...interface{}) {
		fmt.Println("Job2: Time's up! @", time.Now().UTC(), params)
	}

	// Schedule jobs
	// Job1: Runs every 2 seconds, starting 2 seconds after initialization
	scheduler.Every(2).Seconds().NotImmediately().Run(job1)
	
	// Job2: Runs every second
	scheduler.Every(1).Seconds().Run(job2)

	// Job1: Additionally runs every 5 minutes
	scheduler.Every(5).Minutes().Run(job1)

	// Job1: Runs daily
	scheduler.Every().Day().Run(job1)

	// Job2: Runs every Monday at 08:30
	scheduler.Every().Monday().At("08:30").Run(job2)

	// Prevent program exit
	runtime.Goexit()
}
```
## Features
**Easy-to-Read Syntax:** Fluent interface for defining job schedules.

**Flexible Scheduling:** Supports recurrent job scheduling with custom intervals.

**Immediate and Delayed Execution:** Choose whether to run jobs immediately or after the first interval passes.
## How Does It Work?
Scheduler creates a Job struct upon chaining method calls. A goroutine monitors each Job with the following behavior:

On reaching the scheduled time, the job is executed.
If the `SkipWait` channel is signaled, the job is executed immediately.
If the `Quit` channel is signaled, the job execution is halted.
## Delayed Execution for Recurrent Jobs
Recurrent jobs are executed immediately by default. Use the `.NotImmediately()` method to postpone execution until the scheduled time after the job is first run:
```go
// The job will run 5 minutes after .Run() is called, not immediately
scheduler.Every(5).Minutes().NotImmediately().Run(job)
```
## Installation
To include Scheduler in your project, run:
```shell
go get github.com/lonrun/scheduler
```
## License
Scheduler is distributed under the MIT License. See the LICENSE file for more details.
