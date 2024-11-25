# Full-Stack FastAPI and React Template - DevOps November CV Challenge

Welcome to the **Full-Stack FastAPI and React Template** repository! This project serves as a demo application for the **DevOps November Resume Challenge**, where participants will deploy a full-stack application with a **FastAPI** backend and **ReactJS** frontend using **ChakraUI**. Additionally, participants will set up monitoring and logging tools, configure a reverse proxy, and deploy the application to a cloud platform, showcasing their end-to-end DevOps skills.

---

## Challenge Overview

As part of this challenge, your primary goal is to:

1. Dockerize a full-stack application with a FastAPI backend and React frontend.
2. Configure and deploy monitoring and logging tools including Prometheus, Grafana, Loki, Promtail, and cAdvisor.
3. Set up a reverse proxy for application routing.
4. Deploy the application to a cloud platform with proper domain configuration.
5. Submit a working repository with detailed documentation and screenshots of your deployed application.

---
## Project Implementation
<img src=".assets/Screenshot (493).png" width="45%"></img> <img src=".assets/Screenshot (478).png" width="45%"></img> 

### Project Details
The project is structured into 4 directories:
- **frontend**: Contains the ReactJS application.
- **backend**: Contains the FastAPI application and PostgreSQL database integration.
- **monitoring**: Contains config files for the monitoring services.
- **nginx**: contains config files for nginx proxy manager.

The project uses two compose files
- **compose.yml**: This is the compose file for the application stack. This deploys the frontend, backend, postgresql and adminer.
- **compose.monitoring.yml**: This is the compose file for the monitoring stack. This deploys prometheus, grafana, cadvisor, loki and promtail. 
- The two compose files are deployed simultaneously with a single command **"docker compose up -d"**

The `include` directive allows for modular management of monitoring services without cluttering the main Compose file.

**Monitoring config files**: The monitoring folder include the following config for monitoring services:
- loki-config.yml: 
- prometheus.yml
- promtail-config.yml

## Setup
- The following domains point to the server.
    - `cv1.drintech.online`
    - `www.cv1.drintech.online`
    - `db.cv1.drintech.online`
    - `www.db.cv1.drintech.online`
    - `nginx.cv1.drintech.online`
    - `www.cv1.drintech.online`
- clone the project
  ```sh
   git clone https://github.com/DrInTech22/cv-challenge01.git
   cd cv-challenge01
   ```
- Adjust the database credentials in backend folder .env to your postgresql container credentials.
- run the project
  ```sh
  docker compose up -d
  ```
- access the NPM on your browser using your public-ip
  ```sh
   your_public_ip:8090
   ```
- login using NPM default login 
  ```
  username: admin@example.com
  password: changeme
  ```
  you will be prompted to change the password after login

- generate ssl certificates for your subdomains in the following **order below** using **lets encrypt**. 
  - **cv1.drintech.online**
  - **db.cv1.drintech.online**
  - **nginx.cv1.drintech.online**
- Take note of the ssl cert path for each domain and modify the nginx.conf file accordingly.

## Secure deployment setup
- uncomment `#- ./nginx/nginx.conf:/data/nginx/custom/http_top.conf` in the `compose.yml` file. This maps nginx.conf file on NPM.
    ```
    volumes:
    - data:/data
    - letsencrypt:/etc/letsencrypt
    # -./nginx/nginx.conf:/data/nginx/custom/http_top.conf
    ```
- nginx.conf sets up proxy host for the sub-domains, **www to non-www redirection** and **http to https redirection**.
- change the URL in the frontend .env to a secure URL `https://cv1.drintech.online`
- restart the application
  ```sh
  docker compose up frontend nginx --force-recreate
  ```
- **Verify the services are running and all path are accessible**:
   - **FastAPI Backend**: cv1.drintech.online/api
   - **FastAPI Backend Docs**: cv1.drintech.online/docs
   - **FastAPI Backend Redoc**: cv1.drintech.online/redoc
   - **Prometheus**: cv1.drintech.online/prometheus
   - **Grafana**: cv1.drintech.online/grafana
   - **Node.js Frontend**: cv1.drintech.online
   - **Adminer**: db.cv1.drintech.online
   - **Nginx Proxy Manager**: nginx.cv1.drintech.online

- test http to https redirection and www to non-www redirection are working
  - www.cv1.drintech.online
  - https://www.cv1.drintech.online
  - http://www.db.cv1.drintech.online

<img src=".assets/Screenshot (498).png" width="45%"></img> <img src=".assets/Screenshot (491).png" width="45%"></img> 
<img src=".assets/Screenshot (494).png" width="45%"></img> <img src=".assets/Screenshot (493).png" width="45%"></img> 

## Monitoring Setup
- After all applications are successfully accessible through the custom domain, grafana is then configured for visualizations of metric and logs.
- Login to grafana using the default credentials.
    ```
    password: admin
    username: admin
    ```
- Set prometheus as a data source with the URL `http://prometheus:9090/prometheus`
- Set loki as a data source with the URL `http://loki:3100`
- Import container metrics dashboard using ID `19792`
- create a new dashboard. Go to settings, set the title and set the following variables.
    - name: `container` display name: `container` variable type: `Query` label value: `container_name`
    - name: `container_search` display name: `search` variable type: `textbox` 
    - name: `severity` display name: `severity` variable type: `custom` custom values: `info, warn, error`
    - name: `NodeLogs` display name: `NodeLogs` variable type: `Query` label value: `filename`
    - name: `varlog_search` display name: `NodeLog filter` variable type: `textbox` 
    - 
- create new visualization for container logs with the following query:
    ```
    {container_name="$container"} |~ `$container_search` | logfmt level | (level =~ `$severity` or level= "")
    ```
- create a new visualization for Node logs with the following query:
    ```
    {job="varlogs"} |~ `$varlog_search`
    ```

With this setup, you'll be able to search the container logs and also filter based on severity of the logs (info,warning or error). The same functionality apply to the Node logs, you can search through the logs using any preferred keyword.
 
<img src=".assets/Screenshot (494).png" width="45%"></img> <img src=".assets/Screenshot (495).png" width="45%"></img> 
<img src=".assets/Screenshot (499).png" width="45%"></img> <img src=".assets/Screenshot (500).png" width="45%"></img> 




