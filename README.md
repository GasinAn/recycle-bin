# recycle-bin
Simple and Stupid Linux Bash Recycle Bin

```bash
del() {
  while [ $# -gt 0 ]; do
    olddir=$(dirname $(realpath -s $1))
    oldname=$(basename $(realpath -s $1))
    newdir=~/recycle_bin/$(date -I)
    newname=$(date -Ins)-$oldname
    mvbksh=$newdir/.mvbk.$newname.sh

    mkdir -p $newdir
    mv $1 $newdir/$newname

    echo "#!/bin/bash

          if [ -e $olddir/$oldname ]; then
            echo '$olddir/$oldname exists!'
            echo 'Fail to move back.'
            exit 1
          fi

          mkdir -p $olddir
          mv $newdir/$newname $olddir/$oldname
          rm $mvbksh
          " | sed 's/^          //g' >> $mvbksh
    chmod u+x $mvbksh

    shift
  done
}
```
