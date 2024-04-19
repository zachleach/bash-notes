```bash
note_dirs=(
	'gaming'
	'room'
	'csmc'
	'mc'
	'mtg' 
	'cf'
	'os'
	'swe'
	'reading'
	'therapy'
	'relationships'
	'learning'
)

function notes() {
	dir="" base="$(basename $(pwd))"
	[[ " ${note_dirs[@]} " =~ " ${base} " ]] && dir="${base}"
	[[ " ${note_dirs[@]} " =~ " ${1} " ]] && dir=${1}
	
	week_num=$(date +%U)		# week number [00..53]
	week_num=${week_num#0}		# strip the leading zero for non-octal arithmetic
	((week_num++))				
	week_num=$(printf "%02d" ${week_num})	
	year_num=$(date +%Y)
	cd ~/notes/${dir} && vi +${week_num} ${year_num}.md
}

function note() {
	dir="" base="$(basename $(pwd))"
	[[ " ${note_dirs[@]} " =~ " ${base} " ]] && dir="${base}"
	[[ " ${note_dirs[@]} " =~ " ${1} " ]] && dir=${1}

	week_num=$(date +%U)		# week number [00..53]
	week_num=${week_num#0}		# strip the leading zero for non-octal arithmetic
	((week_num++))				
	week_num=$(printf "%02d" ${week_num})	
	week_str=$(date +%Y)-${week_num}
	day_num=$((($(date +%u) + 1) % 8))
	cd ~/notes/${dir} && vi +${day_num} ${week_str}.md
}

function n() {
	dir="" base="$(basename $(pwd))"
	[[ " ${note_dirs[@]} " =~ " ${base} " ]] && dir="${base}"
	[[ " ${note_dirs[@]} " =~ " ${1} " ]] && dir=${1}

	date=$(date +%Y-%m-%d)
	cd ~/notes/${dir} && vi ${date}.md
}
```
