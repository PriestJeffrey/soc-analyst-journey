# Bandit Level 0 → 1

**Date:** 05/06/2026
**Objective:** Find password stored in - file

## Commands Used
- ls → listed files in home directory
- cat ./- → read file contents

## What Failed
- Initially tried cat - but nothing happened. my cursor was just blinking meaning it was waiting for an input.
- finally used ./ which worked. it treated the ( - ) as a file path.

## What Worked
- cat ./- → revealed the password directly

## Key Lesson
- Always run ls first to see what's in your directory
- cat reads file contents directly
- In Linux, dash ( - ) means standard input when used with cat
- ./ prefix forces Linux to treat it as a filename in the 
  current directory instead of a special symbol

## Password 
263JGJPfgU6LtdEvgfWU1XP5yac29mFx
