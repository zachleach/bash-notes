```sh
declare -a note_dirs
mapfile -t note_dirs < ~/.notes_file

function notes() {
	dir="" base="$(basename $(pwd))"
	[[ " ${note_dirs[@]} " =~ " ${base} " ]] && dir="${base}"
	[[ " ${note_dirs[@]} " =~ " ${1} " ]] && dir=${1}

	week_num=$(date +%U)		# week number [00..53]
	week_num=${week_num#0}		# strip the leading zero for non-octal arithmetic
	((week_num++))	
	vi_offset=$(((week_num + 1) / 2))		# e.g., { 1: 1, 3: 2, 5: 3, 7: 4, ... }
	year_num=$(date +%Y)
	cd ~/notes/${dir} && vi +${vi_offset} ${year_num}.md
}

function note() {
	dir="" base="$(basename $(pwd))"
	[[ " ${note_dirs[@]} " =~ " ${base} " ]] && dir="${base}"
	[[ " ${note_dirs[@]} " =~ " ${1} " ]] && dir=${1}

	week_num=$(date +%U)		# week number [00..53]
	week_num=${week_num#0}		# strip the leading zero for non-octal arithmetic
	((week_num++))	

	o_arr=(0 2 3 4 5 6 7 1)		# vi_offset = arr[date +%u] (for odd weeks)
	e_arr=(0 9 10 11 12 13 14 8)	# vi_offset = arr[date +%u] (for even weeks)
	vi_offset=${o_arr[$(date +%u)]}
	if [[ $((${week_num} % 2)) -eq 0 ]]; then	# if even week, 2 things:
		vi_offset=${e_arr[$(date +%u)]}	
		((week_num--))			# e.g., week 4 belongs to week 3 (week 4 - 1)
	fi
	week_num=$(printf "%02d" ${week_num})	
	week_str=$(date +%Y)-${week_num}

	cd ~/notes/${dir} && vi +${vi_offset} ${week_str}.md
}

function n() {
	dir="" base="$(basename $(pwd))"
	[[ " ${note_dirs[@]} " =~ " ${base} " ]] && dir="${base}"
	[[ " ${note_dirs[@]} " =~ " ${1} " ]] && dir=${1}

	date=$(date +%Y-%m-%d)
	cd ~/notes/${dir} && vi ${date}.md
}
```

