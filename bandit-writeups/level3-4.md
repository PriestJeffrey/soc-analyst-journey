# Bandit Level 3-4

**Date:** 05/06/2026
**Objective:** Find password stored in a hidden file in the inhere directory

## Commands Used
- ls → listed files in home directory
- ls -la -> listed all hidden files 
- cd inhere -> navigated into the directory called inhere
- ls -la -> listed all hidden files in the directory called inhere
- cat ./"...Hiding-From-You" -> revealed the password

## What Failed
- i placed a space between cat ./ "...Hiding-From-You". and that was a mistake but it worked accidentally because cat processed the second argument separately.

## What Worked
- cat ./"...Hiding-From-You" → revealed the password directly

## Key Lesson
- Always run ls first to see what's in your directory
- Always run ls -la to see hidden files
- cat reads file contents directly
- cat ./ reads files that begin with special characters and spaces in filenames
 by specifying an explicit path.

## Password
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
