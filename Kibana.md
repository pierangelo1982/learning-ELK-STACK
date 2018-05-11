### Kibana

# install
```
sudo apt.get update
```

```
sudo apt-get install kibana
```

if you are in virtualbox edit /etc/kibana/kibana.yml:
```
sudo vi /etc/kibana/kibana.yml
```
edit and change server.host: "localhost"

to

server.host: "0.0.0.0"

reload Kibana and stat it as a service:

```
sudo /bin/systemctl daemon-reload
```
```
sudo /bin/systemctl enable kibana.service
```
```
sudo /bin/systemctl start kibana.service
```