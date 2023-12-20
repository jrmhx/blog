---
title: "Shell Pipes: Simplifying Command Line Tasks"
date: 2023-12-17T00:00:00+11:00
# weight: 1
# aliases: ["/first"]
tags: ["shell", "CLI"]
author: "Jeremiah Hsing"
# author: ["Me", "You"] # multiple authors
categories: ["Programming", "Linux Shell"]
draft: false
---

Shell pipes are a powerful feature of command-line interfaces in Unix and Unix-like systems, including Linux and macOS. They allow you to pass the output of one command directly into another command, creating a "pipeline" of operations. This can greatly simplify your tasks and enable efficient data processing. In this post, we'll explore the basics of shell pipes and see some practical examples.

## What is a Shell Pipe?

A shell pipe, denoted by the `|` symbol, takes the output (stdout) of one command and uses it as the input (stdin) for the next command. This chaining of commands allows you to perform complex tasks with a combination of simple commands.

## Basic Example of Using a Pipe

Consider a situation where you have a file named `names.txt` containing a list of names, and you want to find out how many names are in the file. You can use a combination of `cat`, `grep`, and `wc` commands:

```bash
cat names.txt | grep -v "^#" | wc -l
```
In this command:

- `cat` names.txt displays the content of `names.txt`.
- `grep -v "^#"` filters out lines starting with `#` (comments, for instance).
- `wc -l` counts the number of lines.
- The pipe `|` passes the output of each command to the next.

Pipes are particularly useful for sorting and filtering data. For example, if you want to list all files in the current directory, sorted by file size:

```bash
ls -l | sort -nk5
```
This command:

- `ls -l` lists files in long format.
- `sort -nk5` sorts the output numerically (-n) based on the fifth column (-k5), which is the file size.

You can also chain multiple pipes together. Suppose you want to check the most used words in a text file:

```bash
cat article.txt | tr ' ' '\n' | sort | uniq -c | sort -nr | head -10
```
This breaks down as:

- `cat article.txt` displays the file content.
- `tr ' ' '\n'` replaces spaces with new lines, putting each word on a separate line.
- `sort` sorts the words alphabetically.
- `uniq -c` counts the occurrences of each word.
- `sort -nr` sorts the word count in descending order.
- `head -10` shows the top 10 results.

## Conclusion
Shell pipes are a simple yet powerful tool for processing and manipulating data on the command line. By understanding and using pipes, you can combine basic commands to perform complex tasks efficiently.
