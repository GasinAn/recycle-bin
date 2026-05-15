# recycle-bin
Simple and Stupid Linux Bash Recycle Bin

```bash
rbdir=~/.recycle_bin
del() {
    while [ "$#" -gt 0 ]; do
        if [ ! -e "$1" ]; then
            echo "\"$1\"" 'does not exist!'
            echo 'Fail to delete.'
            shift
            continue
        fi

        local oldpath=$(realpath -s -- "$1")
        local olddir=$(dirname -- "$oldpath")
        local oldname=$(basename -- "$oldpath")
        local newdir="$rbdir"/$(date -I)
        local newname=$(date -Ins)-"$oldname"
        local newpath="$newdir"/"$newname"
        local new2old="$newdir"/.new2old."$newname"

        mkdir -p -- "$newdir" &&
        printf '%s' "$oldpath" > "$new2old" &&
        chmod 444 -- "$new2old" &&
        mv -- "$oldpath" "$newpath"

        shift
    done
}
mvbk() {
    while [ "$#" -gt 0 ]; do
        if [ ! -e "$1" ]; then
            echo "\"$1\"" 'does not exist!'
            echo 'Fail to move back.'
            shift
            continue
        fi

        local newpath=$(realpath -s -- "$1")
        local newdir=$(dirname -- "$newpath")
        local newname=$(basename -- "$newpath")
        local new2old="$newdir"/.new2old."$newname"

        if [ ! -e "$new2old" ]; then
            echo 'Cannot find the original path!'
            echo 'Is' "\"$1\"" 'in the recycle bin?'
            echo 'Fail to move back.'
            shift
            continue
        fi

        local oldpath=$(cat -- "$new2old")
        local olddir=$(dirname -- "$oldpath")

        if [ -e "$oldpath" ]; then
            echo 'Original path' "\"$oldpath\"" 'exists!'
            echo 'Fail to move back.'
            shift
            continue
        fi

        mkdir -p -- "$olddir" &&
        mv -- "$newpath" "$oldpath" &&
        rm -f -- "$new2old"

        shift
    done
}
```

## License

[GLWT(Good Luck With That) Public License](https://github.com/GasinAn/recycle-bin/blob/main/LICENSE)
