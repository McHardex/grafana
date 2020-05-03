## Grafana
Grafana, mySQL, prometheus and wmi exporter

Grafana setup

- Download grafana from dockerhub
  - `docker pull grafana/grafana`

- Start grafana in detached mode using this script
  - `docker run -d --name=grafana -p 3000:3000 grafana/grafana`

- Grafana user setup and defaults setup like changing admin name and password
  ```
  docker exec -it -u root grafana bash
  vi /etc/grafana/grafana.ini
  or change from the grafana gui - profile settings
  ```

- Download a docker image database(mySQL - any db works)
  - `docker pull mysql`

- mySQL bash
  - `docker exec -it some-mysql bash`

- start the db
  - `docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql`
  - `default port: 3306`

- connect to the db cli
  - `docker exec -it <name of image> mysql -p`

- create some dbs and table as desired
- go back in history linux
  -  `history 2000 | grep 'link'`

- create a datasource on grafana and connect to the mysqldb using the linked db ip address
  - link mysql with grafana
    - `docker run -d --name=grafana --link mysql -p 3000:3000 grafana/grafana`
    - check the linked ip address in grafana
      - `docker exec -it -u root grafana bash`
      - `cat ../../../etc/hosts`

- create basic dashboard from mysqldb

- setup grafana email notification channel
  ```
  - docker exec -it -u root grafana bash
  - vi /etc/grafana/grafana.ini
  - ?smtp to look for the smtp section
  ```
- SETTING UP WINDOW METRICS WITH WMI EXPORTER, PROMETHEUS AND GRAFANA
  - [wmi prometheus grafana setup for server metrics scraping](https://devconnected.com/windows-server-monitoring-using-prometheus-and-wmi-exporter/)

- Download prometheus docker image
  ```
  - docker pull prom/prometheus
  - docker run -d --name prometheus -p 9090:9090 prom/prometheus
  - prometheus gui(localhost:9090)
  ```
- Download WMI exporter to scrap window metrics
  - go to services on windows to start/stop the exporter as a service
  - server metric should now be live on (http://localhost:9182/metrics)

- Binding Prometheus to the WMI exporter
  ```
  - docker exec -it -u root prometheus sh
  - vi /etc/prometheus/prometheus.yml
  - go to prometheus config file - /etc/prometheus/prometheus.ym
  ```
  ```
  scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  	- job_name: 'prometheus'

    	# Careful, the scrape timeout has to be lower than the scrape interval.
    	scrape_interval: 6s
    	scrape_timeout: 5s
    	static_configs:
      		- targets: ['<hostIP>:<wmi-port>']
  ```
  - restart prometheus
    - `docker restart prometheus`

- Import Grafana Dashboard
  - search for beautiful node/windows exporter
  - create prometheus data source
  - data source config
    ```
    - url: http://<hostIP>:<prometheus-port>
    - scrape interval: 6s
    - Query timeout: 5s
    ```
