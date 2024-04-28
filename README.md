# AWS Cloudfront Access Logs to Opensearch

This Docker Compose file sets up an OpenSearch cluster along with Logstash for data processing and OpenSearch Dashboards for visualization.

## Prerequisites
- Docker Engine installed
- Docker Compose installed

## Usage
1. Clone this repository.
2. Navigate to the directory containing the `docker-compose.yml` file.
3. Run the following command to start the services:

    ```
    docker-compose up -d --wait
    ```

4. Once the services are up and running, you can access OpenSearch Dashboards at `http://localhost:5601`.
5. Use OpenSearch Dashboards to explore and visualize your data.

## Services
### 1. OpenSearch Node
- **Image:** `opensearchproject/opensearch:latest`
- **Container Name:** `opensearch-node`
- **Ports:** 
  - `9200:9200` for HTTP REST API
  - `9600:9600` for monitoring purposes
- **Environment Variables:**
  - `cluster.name`: Name of the OpenSearch cluster
  - `node.name`: Name of the OpenSearch node
  - `discovery.seed_hosts`: Hosts to seed the discovery process
  - `cluster.initial_cluster_manager_nodes`: Initial cluster manager nodes
  - `bootstrap.memory_lock`: Lock memory on startup
  - `OPENSEARCH_JAVA_OPTS`: Java options for OpenSearch
  - `DISABLE_INSTALL_DEMO_CONFIG`: Disable installation of demo configurations
  - `DISABLE_SECURITY_PLUGIN`: Disable security plugin
- **Health Check:** Uses curl to check if OpenSearch is running.

### 2. Logstash
- **Container Name:** `logstash`
- **Volumes:** 
  - Mounts AWS credentials and Logstash configuration files
- **Environment Variables:**
  - `OPENSEARCH_HOST`: Hostname of the OpenSearch node
  - `OPENSEARCH_PORT`: Port of the OpenSearch HTTP REST API
  - `BUCKET_NAME`: AWS S3 bucket name
  - `BUCKET_PREFIX`: Prefix for objects in the S3 bucket
  - `BUCKET_REGION`: Region of the AWS S3 bucket
  - `AWS_SHARED_CREDENTIALS_FILE`: Location of AWS credentials file
- **Dependencies:** Depends on OpenSearch node being healthy.
  
### 3. OpenSearch Dashboards
- **Image:** `opensearchproject/opensearch-dashboards:latest`
- **Container Name:** `opensearch-dashboards`
- **Ports:** 
  - `5601:5601` for accessing the Dashboards UI
- **Environment Variables:**
  - `OPENSEARCH_HOSTS`: Hosts of the OpenSearch cluster
  - `DISABLE_SECURITY_DASHBOARDS_PLUGIN`: Disable security plugin for OpenSearch Dashboards
- **Dependencies:** Depends on OpenSearch node being healthy.
- **Health Check:** Uses curl to check if OpenSearch Dashboards is running.

### 4. Health Check
- **Image:** `busybox`
- **Container Name:** `healthcheck`
- **Dependencies:** Depends on OpenSearch node, OpenSearch Dashboards, and Logstash.
- **Command:** Sleeps indefinitely to keep the container running.

## Note
- Make sure to adjust configurations according to your environment and requirements.
- This setup is for demonstration purposes and may need modifications for production use.
