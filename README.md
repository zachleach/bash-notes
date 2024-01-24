```bash
default_dir='~/notes'

# usage: $ weeks [optional note directory]
function weeks() {
	dir=""
	case $1 in
		"")
			dir=${default_dir}
			;;
		*)
			echo 'invalid note directory'
			return
			;;
	esac
	
	# change to the given directory and open top-level file consisting of links to all week notes
	week_num=$(($(date +%U) + 1))
	year_num=$(date +%Y)
	cd ${dir} && vi +${week_num} ${year_num}.md
}

# usage: $ notes [optional note directory]
function notes() {
	dir=""
	case $1 in
		"")
			dir=${default_dir}
			;;
		*)
			echo 'invalid note directory'
			return
			;;
	esac

	# change to the given directory and open week note consisting of links to all daily notes in current week
	week_num=$(($(date +%U) + 1))
	week_num=$(printf "%02d" ${week_num})
	week_str=$(date +%Y)-${week_num}
	day_num=$((($(date +%u) + 1) % 7))
	cd ${dir} && vi +${day_num} ${week_str}.md

}

# usage: $ note [optional note directory]
function note() {
	dir=""
	case $1 in
		"")
			dir=${default_dir}
			;;
		*)
			echo 'invalid note directory'
			return
			;;
	esac

	# change to the given directory and open daily note corresponding with the current date
	date=$(date +%Y-%m-%d)
	cd ${dir} && vi ${date}.md
}
```
