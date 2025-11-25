```sh
function notes() {
	cd ~/notes && vi +$(date +%V) $(date +%Y).md
}

function note() {
	cd ~/notes && vi +$(date +%u) $(date +%Y)-$(date +%V).md
}
```
