# Mdadm -快速命令参考

> 原文：<https://dev.to/c33s/mdadm---quick-command-reference-441>

```
# show sync status
cat /sys/block/md0/md/sync_action

# show sync progress
cat /proc/mdstat

# show details
mdadm --detail /dev/md0

# mark faulty
mdadm /dev/md0 -f /dev/sdc1

# remove drive
mdadm /dev/md0 -r /dev/sdc1

# readd drive
mdadm /dev/md0 -a /dev/sdc1 
```

Enter fullscreen mode Exit fullscreen mode

*   [https://www.thomas-krenn.com/en/wiki/Mdadm_checkarray](https://www.thomas-krenn.com/en/wiki/Mdadm_checkarray)
*   [http://bencane.com/2011/07/06/mdadm-manually-fail-a-drive/](http://bencane.com/2011/07/06/mdadm-manually-fail-a-drive/)
*   封面图片由 dgazdik[https://pix abay . com/en/hard-drive-hardware-hard disk-1482561/](https://pixabay.com/en/hard-drive-hardware-harddisk-1482561/)