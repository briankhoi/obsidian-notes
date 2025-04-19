## Introduction
If you have a bunch of computers and a giant task, how do you efficiently distribute the compute needed for the task across the different computers? 

Previously, this was done by programmers tediously writing specific scripts to orchestrate the distribution of tasks to efficiently use their resources (as opposed to the inefficient single computer running). However, with different tasks this does not scale, and you would need people knowledgeable in distributed systems to do this too!

So, in 2004, Google engineers introduced **MapReduce**, a programming paradigm/model to efficiently compute a large task across a distributed environment.

The general idea is that you have an set of key/value pairs as an input that gets split/mapped across different computers and each subproblem/intermediate state which is also a key/value pair is individually computed, then re-assembled/reduced where values are merged with the same intermediate key into the final answer/output.
- The types of input keys and values can be different from the types of output keys and values

**Example**: Word counting task

