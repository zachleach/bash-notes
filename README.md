# Vim-Based Note System

A hierarchical note-taking system using plain markdown files and Vim's `gf` hotkey. Navigate through yearly overview, weekly planning, and daily journaling with predictable line numbers and date-based filenames.

## How It Works

The system combines bash date commands with Vim navigation:

### Entry Points

Two bash functions position your cursor at the current date:

```bash
# Open yearly file, cursor on current week
$ notes

# Open weekly file, cursor on current day
$ note
```

These use bash date substitution to calculate line numbers:
- `vi +$(date +%V) 2025.md` → opens at line matching current week number
- `vi +$(date +%u) 2025-$(date +%V).md` → opens at line matching current day of week

### Navigation Between Files

Once inside a file, use Vim's `gf` (go to file) to navigate:

```bash
$ notes  # Opens yearly file at current week
```

Your cursor lands on a line containing a week filename:

```
2025-01
2025-02
2025-03  ← cursor here (week 3)
```

Press `gf` → opens `2025-03.md`

The weekly file contains daily filenames on the first 7 lines:

```
2025-01-13  ← cursor here (line 1)
2025-01-14
2025-01-15
2025-01-16
2025-01-17
2025-01-18
2025-01-19
```

Navigate to any date and press `gf` → opens that daily file.

Press `Ctrl-6` → returns to previous file.

### The Key Design

**Filenames are dates** (`2025-01.md`, `2025-01-13.md`), which enables:

1. Bash date commands calculate which file to open: `2025-$(date +%V).md`
2. Vim's `gf` opens files by recognizing the filename text under the cursor

**Line numbers = date numbers** (line 1 = week 1 or Monday), which enables:

1. Bash date commands calculate which line to jump to: `vi +$(date +%V)`
2. Predictable structure for navigating within files

## Init Script

Creates a year's worth of files with filenames in predictable positions:

```bash
#!/bin/bash

# init
# 2024.09.22, by @zachleach

YEAR='2025' BASE='2024-12-29'
for w in {01..358..7}; do
	monday=$(date -d "${BASE} + ${w} days" +%Y-%m-%d)
	week_file=${YEAR}-$(date -d ${monday} +%V)
	echo ${week_file} >> ${YEAR}.md
	echo ${week_file} && touch ${week_file}.md
	for d in {0..6}; do
		day=$(date -d "${monday} +${d} days" +%Y-%m-%d)
		echo ${day} >> ${week_file}.md
		echo ${day} && touch ${day}.md
	done
done
```

Creates:
- 52 weekly files: `2025-01.md` through `2025-52.md`
- 365 daily files: `2025-01-06.md`, `2025-01-07.md`, etc.
- Writes week filenames to `2025.md` (one per line)
- Writes daily filenames to each weekly file (first 7 lines)

## Bash Functions

Add to `.bashrc` or `.bash_aliases`:

```bash
function notes() {
	cd ~/notes/ && vi +$(date +%V) 2025.md
}

function note() {
	cd ~/notes/ && vi +$(date +%u) 2025-$(date +%V).md
}
```

**`notes`**: Opens yearly file with cursor on line matching current week number

Example: If today is week 15, `$(date +%V)` returns `15`, so `vi +15 2025.md` opens the file at line 15.

**`note`**: Opens current week's file with cursor on line matching current day of week

Example: If today is Wednesday of week 15, `vi +3 2025-15.md` opens the file at line 3 (Monday=1, Tuesday=2, Wednesday=3).

## File Format

### Yearly File (`2025.md`)

Each line contains a week filename. Add a one-liner summary to review the year at a glance without opening individual files.

Minimal format - just the filename:
```
2025-01
2025-02
2025-03
2025-04
```

With summaries:
```
2025-01 setup note system
2025-02 project planning phase
2025-03 completed milestone
2025-04
```

Line N contains week N's filename.

### Weekly File (`2025-03.md`)

First 7 lines contain daily filenames (Monday-Sunday). Add a one-liner summary to review the week at a glance without opening individual daily files.

Minimal format:
```
2025-01-13
2025-01-14
2025-01-15
2025-01-16
2025-01-17
2025-01-18
2025-01-19
```

With summaries:
```
2025-01-13 focused on component design
2025-01-14 refactored project structure
2025-01-15
2025-01-16 research phase
2025-01-17
2025-01-18
2025-01-19
```

Lines 8+ are for week-level content:
```
2025-01-13
2025-01-14
2025-01-15
2025-01-16
2025-01-17
2025-01-18
2025-01-19

focus for this week
Complete refactoring before new features
Emphasize learning over speed

roadmap
- Component architecture
- Event handling
- Integration tests
```

### Daily File (`2025-01-13.md`)

Free-form. Common pattern is timestamped entries:

```
# 2025-01-13 09:00
Morning planning session
Outlined tasks and priorities

# 2025-01-13 14:30
Progress on refactoring
Found edge cases to handle

# 2025-01-13 18:00
End of day review
Completed component restructure
Tomorrow: focus on event handling
```

## Technical Details

### Bash Date Format Codes

- `%V`: ISO week number (01-53), where week 1 contains the first Thursday
- `%u`: Day of week (1=Monday, 7=Sunday)
- `%Y-%m-%d`: Date in ISO format (2025-01-13)

These enable `$(date +%V)` to calculate which line to jump to.

### Vim Line Positioning

`vi +N filename` opens `filename` with cursor on line N.

This is why `vi +$(date +%V) 2025.md` works - it expands to something like `vi +15 2025.md`.

### Why Filenames Are Dates

Using date strings as filenames (`2025-01`, `2025-01-13`) enables:

1. **Bash date substitution** to calculate current filename: `2025-$(date +%V).md`
2. **Vim's `gf` hotkey** to open files by placing cursor on the filename text

The system relies on this alignment: the filename that appears in the text is the same filename that bash can construct from the current date.

### How `gf` Works

Vim's `gf` command opens the file whose name is under the cursor. It searches for the file:
1. In the current directory
2. Using paths in `'path'` option
3. With extensions in `'suffixesadd'` option (e.g., `.md`)

When your cursor is on `2025-01-13`, pressing `gf` looks for `2025-01-13.md` in the current directory.

Set `suffixesadd` in `.vimrc` for automatic `.md` extension:
```vim
set suffixesadd+=.md
```

### Timestamp Insertion

Add this function to `.vimrc` for quick timestamp insertion (rounds to nearest 15 minutes):

```vim
nnoremap <C-t> :call InsertRoundedDateTime()<CR>
function! InsertRoundedDateTime()
    let l:now = localtime()
    let l:rounded = l:now + 450 - ((l:now + 450) % (15 * 60))
    let l:formatted = strftime("%Y.%m.%d %H:%M", l:rounded)
    execute "normal i" . l:formatted
endfunction
```

Press `Ctrl-t` in insert mode to insert a timestamp like `2025.01.13 14:30`.

## Workflow Example

Typical daily workflow:

```bash
$ note  # Opens week file at today's line
```

Cursor lands on today's date → `gf` → opens daily file → write notes.

`Ctrl-6` → back to week file → navigate to different day → `gf` → different daily file.

Review the week:

```bash
$ notes  # Opens yearly file at current week
```

`gf` on current week → see week overview and todos → `gf` on any date → see daily details.

`Ctrl-6` repeatedly to navigate back up the hierarchy.
