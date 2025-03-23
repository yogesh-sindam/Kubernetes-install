want start use a fluent bit for log sent to RDS database:
in previllage want steps need to do from initial,
########################################################
```
 1. Prerequisites
Before we dive into Fluent Bit setup, make sure you have:

✅ RDS Database (MySQL/Postgres) ready
✅ Fluent Bit installed on your server or Kubernetes
✅ DB credentials (hostname, port, user, password, database name)
```
2. Install Fluent Bit
If you’re running on a server (Linux):

```
# For Ubuntu/Debian
sudo apt-get update && sudo apt-get install -y fluent-bit

# For CentOS/RedHat
sudo yum install -y fluent-bit
```

