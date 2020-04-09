```shell 
docker run -i -t -p 5272:80 --name onlyoffice \
-v /opt/onlyoffice/logs:/var/log/onlyoffice \
-v /mnt/hdd0/onlyoffice:/var/www/onlyoffice/Data \
onlyoffice/documentserver
```

