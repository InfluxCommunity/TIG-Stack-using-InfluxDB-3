# Modern TIG Stack for InfluxDB 3

TIG Stack is an arconym for the following three open source technologoies that seamless work togther to collect, store, analyze and monitor real time data from almost anything such as servers, APIs, IoT devices or even your smart coffee machine!

1. **T**elegraf to collect system metrics and write to InflxDB
2. **I**nfluxDB 3 (Core or Enterprise version) as the timeseries database
3. **G**rafana as the data visualization tool that frequently queries metrics from InfluxDB 3 tables.

![TIG Stack](https://github.com/InfluxCommunity/TIG-Stack-using-InfluxDB-3-Core/blob/main/TIG.drawio-4.png)

## Pre-requisite:

1. Docker should be installed on your local machine. We will rely on the [docker-compose.yaml](docker-compose.yml) file to configure and install Telegraf, InfluxDB 3 Core/Enteprise and Grafana docker images.
2. Git for version control (optional)
3. Editor

# Steps:

## 1. Clone the repository
```sh
git clone https://github.com/InfluxCommunity/TIG-Stack-using-InfluxDB-3.git
cd TIG-Stack-using-InfluxDB-3
```

## 2. Start InfluxDB 3 

InfluxDB 3 Core & Generate Opreator Token

```sh
docker-compose up -d influxdb3-core
docker-compose exec influxdb3-core influxdb3 create token --admin
```

## 3. Update .env file

Open [.env](.env) file and paste the token string for "INFLUXDB_TOKEN" enviornment variable.

## 4. Start the remaining services of the TIG Stack in Docker

**First make sure Docker application is up and running on your local machine.
**
```sh
docker-compose up -d telegraf
docker-compose up -d grafana
```

## 5. Verify the Stack

Check Telegraf Logs
```sh
docker-compose logs telegraf
```
Check InfluxDB 3 Logs & See Telegraf generated Tables

```sh
docker-compose logs influxdb3-core
docker-compose exec influxdb3-core influxdb3 query "SHOW TABLES" --database local_system --token REPLACE_WITH_YOUR_TOKEN_STRING
```

## 6. Setup & View Grafana Dashboard

- Open localhost:3000 from your browser 
- Login with credentials from .env (default: admin/admin)
- Add Data Source : 
  - Type: InfluxDB
  - Language : SQL
  - Database: Paste the string value for INFLUXDB_BUCKET enviornment variable from your .env file
  - URL: http://influxdb3-core:8181 for connecting to InfluxDB 3 Core 
  - URL: http://influxdb3-enterprise:8181 for connecting to InfluxDB 3 Enterprise
  - Dataabse: Find this inside the .env file (bucker name is same as database name)
  - Token: Paste the string value for INFLUXDB_TOKEN enviornment variable from your .env file and toggle **Insecure Connection to ON**
- Add Data Visualization : Dashboards > Create Dashboard - Add Visualization > Select Data Source > InfluxDB_3_Core 
- In the query 'builder' paste and run the following SQL query to see the visualization of the data collected via Telegraf, written to InfluxDB 3.
```sql
SELECT "cpu", "usage_user", "time" FROM "cpu" WHERE "time" >= $__timeFrom AND "time" <= $__timeTo AND "cpu" = 'cpu0'
```
![TIG Stack](https://github.com/InfluxCommunity/TIG-Stack-using-InfluxDB-3-Core/blob/main/Grafana_screenshot.png)

## 7. Stopping the TIG Stack & Removing Data

### Stop Services
```sh
docker-compose down
```
### Stop and Remove Volumes (Destroys All Data)
```sh
docker-compose down -v
```
