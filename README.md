```bash
# relative to ~/notes
note_dirs=(
	'os'
	'networks'
	'algos'
	'swe'
)

# usage: $ year [optional note directory]
function year() {
	dir=""
	for d in ${note_dirs[@]}; do 
		if [[ ${1} == ${d} ]]; then dir=${d}; fi
	done

	# change to the given directory and open top-level file consisting of links to all week notes
	week_num=$(($(date +%U) + 1))
	year_num=$(date +%Y)
	cd ~/notes/${dir} && vi +${week_num} ${year_num}.md
}

# usage: $ notes [optional note directory]
function notes() {
	dir=""
	for d in ${note_dirs[@]}; do 
		if [[ ${1} == ${d} ]]; then dir=${d}; fi
	done

	# change to the given directory and open week note consisting of links to all daily notes in current week
	week_num=$(($(date +%U) + 1))
	week_num=$(printf "%02d" ${week_num})
	week_str=$(date +%Y)-${week_num}
	day_num=$((($(date +%u) + 1) % 7))
	cd ~/notes/${dir} && vi +${day_num} ${week_str}.md

}

# usage: $ note [optional note directory]
function note() {
	dir=""
	for d in ${note_dirs[@]}; do 
		if [[ ${1} == ${d} ]]; then dir=${d}; fi
	done

	# change to the given directory and open daily note corresponding with the current date
	date=$(date +%Y-%m-%d)
	cd ~/notes/${dir} && vi ${date}.md
}
```
