# Want start use a fluent bit for log sent to RDS database in previllage want steps need to do from initial,
########################################################
## 1. Prerequisites
Before we dive into Fluent Bit setup, make sure you have:
```
✅ RDS Database (MySQL/Postgres) ready
✅ Fluent Bit installed on your server or Kubernetes
✅ DB credentials (hostname, port, user, password, database name)
```
## 2. Install Fluent Bit
If you’re running on a server (Linux):

```
# For Ubuntu/Debian
sudo apt-get update && sudo apt-get install -y fluent-bit

# For CentOS/RedHat
sudo yum install -y fluent-bit
```
## 3. Configure Fluent Bit
Let’s create a Fluent Bit config file to ship logs to RDS.

` /etc/fluent-bit/fluent-bit.conf `
```
[SERVICE]
    Flush        5
    Log_Level    info
    Daemon       off

[INPUT]
    Name              tail
    Path              /var/log/*.log
    Tag               app.logs
    Refresh_Interval  5

[FILTER]
    Name                record_modifier
    Match               *
    Record              hostname ${HOSTNAME}

[OUTPUT]
    Name              sql
    Match             app.logs
    Host              <RDS-HOST>
    Port              <RDS-PORT>
    Database          <YOUR-DATABASE>
    User              <YOUR-USERNAME>
    Password          <YOUR-PASSWORD>
    Table             logs
```
# 4. Prepare RDS Database
Connect to your RDS and create a logs table:
```
CREATE TABLE logs (
    id SERIAL PRIMARY KEY,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    hostname VARCHAR(100),
    log_text TEXT
);
```

