一句话扩展swap



一句话扩展6G  swap

```
dd if=/dev/zero of=/mnt/swap bs=6M count=1024 && mkswap /mnt/swap && chmod -R 0600 /mnt/swap && swapon /mnt/swap 

开机挂载
echo "/mnt/swap swap swap defaults 0 0" >> /etc/fstab
```