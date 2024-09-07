```sh
function notes() {
	year_num=$(date +%Y)
	week_num=$(date +%U)	# week number [00..53]
	week_num=${week_num#0}	# strip the leading zero for non-octal arithmetic
	((week_num++))           
	vi_offset=${week_num}

	cd ~/notes && vi +${vi_offset} ${year_num}.md
}

function note() {
	sunday=$(date -d "last Sunday" +%Y-%m-%d)
	day_num=$(($(date +%u) % 7))		#	may need to get patched
	vi_offset=${day_num}

	cd ~/notes/${dir} && vi +${vi_offset} ${sunday}.md
}

function n() {
	today=$(date +%Y-%m-%d)
	cd ~/notes/${dir} && vi ${today}.md
}
```
