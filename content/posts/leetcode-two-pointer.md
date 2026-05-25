---
title: "LeetCode: Two Pointer"
date: 2025-05-25
description: ""
tags: [""]
---

## 2 Sets of Pointers, One for Boundary + Another for Operation

Questions like [557. Reverse Words in a String III](https://leetcode.com/problems/reverse-words-in-a-string-iii) can be done using split. Pure pointer solution involves 2x set of pointers, one to check boundary (i-1 when i corresponds to space), another to flip internally.

## Questions I made awkward implementation

[977. Squares of a Sorted Array](https://leetcode.com/problems/squares-of-a-sorted-array/) - can start from head and tail with correct insert position in result.

[844. Backspace String Compare](https://leetcode.com/problems/backspace-string-compare) - can treat this kind of problem instead of cleaning up out of the loop, treat it as "find next valid character"

[88. Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array) - can iterate on non-negative nums2 index since nums1 is already in correct position

## Already known tricks

* fast vs. slow pointers (insert_idx incremented conditionally)
