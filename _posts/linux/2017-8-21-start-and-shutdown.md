

# start.sh
```
nohup ./agent 2>&1 | /usr/local/sbin/cronolog  ./log/agent-%Y-%m-%d.log&
```

# shutdown.sh
```
ps -ef|grep './agent'|grep -v 'grep'|awk '{print $2}'|xargs  -r kill
```
