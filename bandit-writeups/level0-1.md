# Bandit Level 0 → 1

**Date:** 05/06/2026
**Objective:** Find password stored in readme file

## Commands Used
- ls → listed files in home directory
- cat readme → read file contents

## What Failed ( What I Did Wrong )
- Used `find -name readme` first — unnecessary for a file 
  sitting in the home directory
- `ls` would have been simpler and faster
- Lesson: use the simplest tool first, escalate complexity only when needed

## What Worked
- cat readme → revealed the password directly

## Key Lesson
- Always run ls first to see what's in your directory
- cat reads file contents directly

## Password
ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If
