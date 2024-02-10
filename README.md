```bash
note_dirs=(
	'mtg'
)

function year() {
	dir=""
	for d in ${note_dirs[@]}; do 
		[[ ${1} == ${d} ]] && dir=${d}
	done

	week_num=$(($(date +%U) + 1))
	year_num=$(date +%Y)
	cd ~/notes/${dir} && vi +${week_num} ${year_num}.md
}

function notes() {
	dir=""
	for d in ${note_dirs[@]}; do 
		[[ ${1} == ${d} ]] && dir=${d}
	done

	week_num=$(($(date +%U) + 1))
	week_num=$(printf "%02d" ${week_num})
	week_str=$(date +%Y)-${week_num}
	day_num=$((($(date +%u) + 1) % 8))
	cd ~/notes/${dir} && vi +${day_num} ${week_str}.md
}

function note() {
	dir=""
	for d in ${note_dirs[@]}; do 
		[[ ${1} == ${d} ]] && dir=${d}
	done

	date=$(date +%Y-%m-%d)
	cd ~/notes/${dir} && vi ${date}.md
}
```
