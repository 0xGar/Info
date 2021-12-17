# Updating your project to use Golang modules:

### 1. Make sure GO's environment variable GO111MODULE is off 

> Linux shell: export GO111MODULE=off 

> Windows CMD: set GO111MODULE=off

### 2. Make sure GOPATH is set (Google this if not).

### 3. Navigate to your src directory (where main.go exists), and execute:

> go mod init <random_name>

> go mod tidy

***Tip:*** <random_name> can be anything, but it's good practice for it to follow GitHub structure.

***Important Note:*** If you receive package errors when executing "go mod tidy", 
then you most likely have Github import issues that need fixing.

To fix this issue, replace the broken imports 
with the full GitHub import path (e.g., import ("github.com/disintegration/gift")).

(This problem could occur, for example, if you manually downloaded GitHub projects 
and then locally referenced them. "go mod tidy" will download the GitHub packages
for you if they're missing or not found on your system, so long as you do proper GitHub imports).

### 4. Rename all of your project packages to "<random_name>/myPackage".

For example, image this structure:

-> main.go

--> tools

---> tools.go

With the following package & imports:

main.go file:

> Line 1: package main

> Line 2: import ("tools")

> Line 3: ...

and

tools.go file:

> Line 1: package tools

> Line 2: ...

You would change 'import ("tools")' to 'import "<random_name>/tools"' in main.go.

At this point, you should be good to go.
