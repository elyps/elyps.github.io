cd /mnt/z/ROMS

mksquashfs "Devil May Cry 4.ps3" "Devil May Cry 4.squashfs"

 rsync -avz -e "ssh -p22" \
  "/mnt/z/ROMS/PS3/Devil May Cry 4.squashfs" \
  root@192.168.2.149:/userdata/roms/ps3/
