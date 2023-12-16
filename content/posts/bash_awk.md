---
title: "Understand awk in Linux Shell"
date: 2023-12-17T00:54:00+11:00
# weight: 1
# aliases: ["/first"]
tags: ["shell", "linux", "awk"]
author: "Jeremiah Hsing"
# author: ["Me", "You"] # multiple authors
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
categories: ["Programming"]
draft: false
---

## What is awk?

`awk` is a powerful scripting language and command-line utility for text-processing in Unix/Linux environments. It scans each line of a file, looking for patterns that match, and then processes the line accordingly (kinda like `reduce` in JavaScript but has difference). It's excellent for manipulating structured text data like CSV, logs, or even simple text files.

### Basic Syntax
The basic syntax of awk is:
```bash
awk 'pattern {action}' file
```
- pattern: A condition to match (like regex or conditional expressions). If omitted, awk processes all lines.
- action: Commands to execute when a pattern matches. Actions are enclosed in {}.

### Types of Patterns in awk
1. Regular Expressions: Use regular expressions as patterns to match lines.
Example: `awk '/error/ {print}'` file.txt prints lines containing "error".

2. Relational Expressions: These include comparisons of numbers or strings.
Example: `awk '$2 > 100 {print}'` file.txt prints lines where the second field is greater than 100.

3. Compound Patterns: Combine patterns using logical operators (`&&`, `||`, `!`).
Example: `awk '$1 == "start" && $3 > 50 {print}' file.txt` prints lines where the first field is "start" and the third field is over 50.

4. Range Patterns: Match lines from the start pattern to the end pattern.
Example: `awk '/start/, /end/ {print}' file.txt` prints lines from "start" to "end".

5. Special Patterns `BEGIN` and `END`:
`BEGIN`: Executed before reading any input lines, often for initialization.
`END`: Executed after all lines are processed, used for summaries or conclusions.

## Example Problem: Word Frequency

Let's use [LeetCode 192. Word Frequency](https://leetcode.com/problems/word-frequency/description/): write a bash script to calculate the frequency of each word in a text file `words.txt` as a example.

The solution of this problem is straight forward, bascially in 3 steps: 
1. Read the file `words.txt`;
2. Walk through each word, record/update the frequencey;
3. Sort and print out the result.

However, implementing this in a shell script can be hard, especially for people who aren't familiar with Linux Shell Script. It's even more challenging if you want to write it in a single line of script code (and yeah all Linux developers do it cause it looks cool af XD)

But no worries, let's break the damn thing down with [Shell Pipe](/blog/posts/bash_awk/). If you don't know what it is, I have another [blog](/blog/posts/bash_awk/) about that and feel free to check it out.


## Solution:

### Read the File

- `cat words.txt`

   This command reads the content of the file words.txt.
   cat is short for concatenate. When used with a file name, it displays the content of the file.

- `echo -e $(...)`

   The `$(...)` is command substitution, which means it executes the command inside the parentheses and then echo outputs the result.
   `echo -e` enables interpretation of backslash escapes. When used with the output of `cat words.txt`, it converts newlines into spaces. This effectively puts all the words on a single line.

### Update frequencey

This awk script is the core part of the command.

`for(i=1; i<=NF; i++){words[$i]++}`: This loop iterates over all fields (words) in a line. `NF` is a built-in variable in awk that represents the number of fields in the current record (line). For each word, it increments the count in the associative array words.

`END {for(w in words) print w, words[w]}`: After processing all lines, this part executes. It iterates through each index (word) in the array words and prints the word and its frequency.

### Sort and Print

`sort -k2 -n -r`

This sorts the output from the awk command.
`-k2` tells sort to sort based on the second column (which is the word frequency).
`-n` means to sort numerically (since word counts are numbers).
`-r` sorts in reverse order, so you get a list from the most frequent to the least frequent word.


```bash
echo -e $(cat words.txt) | awk '{for(i=1; i<=NF; i++){words[$i]++}} END {for(w in words) print w, words[w]}' | sort -k2 -n -r
```