# recycle-bin
Simple and Stupid Linux Bash Recycle Bin

```bash
del() {
    while [ $# -gt 0 ]; do
        oldpath=$(realpath -s "$1")
        olddir=$(dirname "$oldpath")
        oldname=$(basename "$oldpath")
        newdir=~/.recycle_bin/$(date -I)
        newname=$(date -Ins)-"$oldname"
        mvbksh=$newdir/.mvbk."$newname".sh

        mkdir -p $newdir
        mv "$1" $newdir/"$newname"

        mvbksh_lines=(
            "#!/bin/bash"
            ""
            "set -e"
            ""
            "if [ -e '$olddir/$oldname' ]; then"
            "    echo '\"$olddir/$oldname\" exists!'"
            "    echo 'Fail to move back.'"
            "    exit 1"
            "fi"
            ""
            "mkdir -p '$olddir'"
            "mv '$newdir/$newname' '$olddir/$oldname'"
            "rm '$mvbksh'"
        )
        printf "%s\n" "${mvbksh_lines[@]}" > "$mvbksh"
        chmod u+x "$mvbksh"

        shift
    done
}
```

## License

[GLWT(Good Luck With That) Public License](https://github.com/GasinAn/recycle-bin/blob/main/LICENSE)
