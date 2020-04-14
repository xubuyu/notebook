```shell
docker run -it -p 139:139 -p 445:445 --name samba \
--restart always \
--env USERID="0" \
--env GROUPID="0" \
-v /mnt:/mount \
-d dperson/samba:aarch64 -p \
-r \
-u "Romengine;Rmj.5243" \
-u "Lytine;latiao123" \
-u "guest;12345666." \
-s "Romengine;/mount;yes;no;no;Romengine" \
-s "Lytine;/mount/hdd0/Lytine;yes;no;no;Romengine,Lytine" \
-s "guest;/mount/hdd0/guest;yes;no;no;"
```

