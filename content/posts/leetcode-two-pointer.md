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

[Valid Word Abbreviation](https://neetcode.io/problems/valid-word-abbreviation) - in some questions esp. stack, integer is handled one by one. Here, consume all of them (including ones with preceding zeros which would be invalid) then comparison becomes trivial

1578. Minimum Time to Make Rope Colorful <---- TODO: interesting solution

## Already known tricks

* fast vs. slow pointers (insert_idx incremented conditionally)
* 3x flips (2x internal, 1x full) - [189. Rotate Array](https://leetcode.com/problems/rotate-array)
* move smaller pointer - [11. Container With Most Water](https://leetcode.com/problems/container-with-most-water)
* stuff small with large whenever possible, as each boat contains max. 2 people. BUT this is different from another question where it can contain any number of people, I think in binary search - [881. Boats to Save People](https://leetcode.com/problems/boats-to-save-people)
* play where we lose min (min token to gain score, max token to gain token) - [948. Bag of Tokens](https://leetcode.com/problems/bag-of-tokens)
