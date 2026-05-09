# recycle-bin
Simple and Stupid Linux Bash Recycle Bin

```bash
rbdir=~/.recycle_bin
del() {
    while [ "$#" -gt 0 ]; do
        oldpath=$(realpath -s "$1")
        olddir=$(dirname "$oldpath")
        oldname=$(basename "$oldpath")
        newdir="$rbdir"/$(date -I)
        newname=$(date -Ins)-"$oldname"
        newpath="$newdir"/"$newname"
        new2old="$newdir"/.new2old."$newname"

        mkdir -p "$newdir"
        mv "$oldpath" "$newpath"
        printf '%s' "$oldpath" > "$new2old"

        shift
    done
}
mvbk() {
    while [ "$#" -gt 0 ]; do
        newpath=$(realpath -s "$1")
        newdir=$(dirname "$newpath")
        newname=$(basename "$newpath")
        new2old="$newdir"/.new2old."$newname"

        if [ ! -e "$new2old" ]; then
            echo 'I suppose that' "\"$1\"" 'is not in the recycle bin!'
            echo 'Did not move back.'
            shift
            continue
        fi

        oldpath=$(cat "$new2old")
        olddir=$(dirname "$oldpath")

        if [ -e "$oldpath" ]; then
            echo "\"$oldpath\"" 'exists!'
            echo 'Fail to move back.'
            shift
            continue
        fi

        mkdir -p "$olddir"
        mv "$newpath" "$oldpath"
        rm "$new2old"

        shift
    done
}
```

## License

[GLWT(Good Luck With That) Public License](https://github.com/GasinAn/recycle-bin/blob/main/LICENSE)
