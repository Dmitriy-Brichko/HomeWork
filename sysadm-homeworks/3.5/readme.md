2. Жесткие ссылки на объект это неограниченное количесво имен для обьекта, а inode (идентификатор) один. Поэтому права доступа и владелец у имен объекта одинаковые.
3. -
4. fdisk /dev/sdb
   n
   p
   1
   2048
   +2G
   n
   p
   2
   4196352
   5242879
   w
5. sfdisk -d /dev/sdb | sfdisk /dev/sdc
6. mdadm --create --verbose /dev/md0 --level=1 --raid-devices=2 /dev/sdb1 /dev/sdc1
7. mdadm --create --verbose /dev/md1 --level=0 --raid-devices=2 /dev/sdb2 /dev/sdc2
8. pvcreate /dev/md0 /dev/md1
9. vgcreate vg01 /dev/md0 /dev/md1 (Здесь что-то пошло не так и создалась группа томов на raid1, прошу обьяснить что не так сделал)
10. lvcreate -L100 vg01
11. mkfs.ext4 /dev/vg01/lvol0
12. mount /dev/vg01/lvol0 /tmp/new/
13. -
14. Вывод lsblk во вложении "14.lsblk.png" (raid1 и raid0 дальше поменялись местами из-за ошибки в 9.)
15. -
16. pvmove /dev/md0 /dev/md1
17. mdadm --fail /dev/md0 /dev/sdb1
18. Во вложении "18.dmesg.png"