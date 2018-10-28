This tool snapshots the directory holding the mirrored debian repository. To use it, you must use zfs and create a zfs pool_apt/mirror mounted on /var/spool/apt-mirror/mirror. The script will create snapshots in /var/spool/apt-mirror/mirror/snap.

It actually does create and maintain the snapshots:

*   week-0
*   week-1
*   working-day-0
*   working-day-1
*   working-day-2
*   working-day-3
*   working-day-4
*   working-day-5
*   working-day-6

**working-day-X** is a snapshot that won't change during the day, it will rotate at the next working day. So if working-day-0 was created on friday, it won't change neither on saturday neither on sunday (as they are not working day). That means that if a machine is pointing on any working-day-X it will see an updated repository only on monday, thuesday, wednesday, thursday, friday.

**week-X** means that it is the snapshot at the beginning of a week. It won't change during the whole week. It will rotate at the beginning of the week.

The idea was to have "lab" machines pointing to working-day-0, testing machines pointing to working-day-1, 5%, 25% and 70% of production machines pointing respectively to working-day-2, working-day-3 and working-day-4. That way, the patch apply to "lab" first, then to "test", then "5%" to "prod" and so on.

For clustering machines we knew that we need to have at least one working. So all the machines belonging to the same cluster where pointing at the same snapshot: week-0. But we will apply the patches, restart service or reboot the machine at different date. For example, the first node on monday and the second node on wednesday.

For information, we do use on our debian machines:

*   needrestart : to automatically restart service or reboot the kernel
*   unattended-upgrades: to automatically install patches
*   create /etc/systemd/system/apt-daily-upgrade.timer.d/central-it-only-on-monday with:
```
[Timer]
OnCalendar=
OnCalendar=Mon *-*-* 06:00
```

We also do not change the repository during the christmass period.

cED
