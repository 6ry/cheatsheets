## Resources
* <http://www.nongnu.org/rdiff-backup/examples.html>
* man rdiff-backup



## Backup

Backup local directory dir1 to local directory dir2
`$ rdiff-backup dir1 dir2`

Backup between local and remote hosts
```
$ rdiff-backup /path/to/local-dir remotehost::/whatever/remote-dir
$ rdiff-backup user@remotehost::/path/to/remote-dir /path/to/local-dir
```

`--print-statistics`
    summary statistics will be printed after a successful backup

`[-v[0-9]|--verbosity [0-9]]`
    verbosity level (0 is totally silent, 3 is the  default,  and  9  is  noisiest).



## Restore backup

`[--restore-as-of|-r] [now|<time-string>|<date-string>]`
see the `TIME FORMATS` section of the manual page

```
$ rdiff-backup -r now         host.net::/remote-dir/file local-dir/file   # as of now
$ rdiff-backup -r 10D         host.net::/remote-dir/file /tmp/file        # 10 days ago
$ rdiff-backup -r 5m4s        host.net::/remote-dir/file /tmp/file        # 5 minutes 4 seconds
$ rdiff-backup -r 2002-03-05  host.net::/remote-dir/file /tmp/file        # March 5th, 2002
```

`--list-increment-sizes`
    List the total size of all the increment and mirror files by time.  This may be  helpful
    in  deciding how many increments to keep, and when to --remove-older-than.  Specifying a
    subdirectory is allowable; then only the sizes of the mirror and  increments  pertaining
    to that subdirectory will be listed.



## Remove diffsets which are too old

`--remove-older-than`

deletes all information concerning file versions which have not been current for 2 weeks
`$ rdiff-backup --remove-older-than 2W host.net::/remote-dir`

The 20B below tells rdiff-backup to only preserve information from the last 20 rdiff-backup sessions.
`$ rdiff-backup --remove-older-than 20B host.net::/remote-dir`



## Include/Exclude Files

`TODO`



## Getting information about the backup directory

`[--list-increments|-l]`
Lists the times of the available versions of the file `out-dir/file`
`$ rdiff-backup -l out-dir/file`

`--list-changed-since`
Lists all the files under `out-dir/subdir` which has changed in the last 5 days
`$ rdiff-backup --list-changed-since 5D out-dir/subdir`

`--list-at-time`
Lists all the files that were present in `out-dir/subdir` 5 days ago
`$ rdiff-backup --list-at-time 5D out-dir/subdir`

`--compare`
Compares the current files in out-dir with the files in in-dir, displaying which ones have changed
`$ rdiff-backup --compare in-dir out-dir`

`--compare-at-time`
Compares in-dir to out-dir as it was 2 weeks ago
`$ rdiff-backup --compare-at-time 2W in-dir out-dir`

`--calculate-average`
`$ rdiff-backup --calculate-average out-dir/rdiff-backup-data/session_statistics*`
