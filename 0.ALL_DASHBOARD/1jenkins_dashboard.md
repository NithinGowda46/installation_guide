## Jenkins Default Port

By default, Jenkins runs on **port 8080**.

After starting the Jenkins service, access it from your browser:

```text
http://<SERVER-IP>:8080
```

Example:

```text
http://192.168.1.100:8080
```

### Verify Jenkins is Listening on Port 8080

```bash
sudo ss -tulnp | grep 8080
```

or

```bash
sudo netstat -tulnp | grep 8080
```