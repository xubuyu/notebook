```shell
docker run -it -p 139:139 -p 445:445 --name samba \
--restart always \
--env USERID="0" \
--env GROUPID="0" \
-v /mnt:/mount \
-d dperson/samba:aarch64 -p \
-r \
-u "Romengine;Rmj.5243" \
-s "Romengine;/mount;yes;no;yes"
```

