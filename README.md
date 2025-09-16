Overview: 

A fork of OSSEC, Wazuh has evolved into a full-fledged SIEM and XDR platform.
Key Features:
Real-time threat detection and file integrity monitoring
Log data analysis and correlation
Compliance reporting (e.g., PCI DSS, HIPAA)
Integration with, Cribl, Elastic Stack (Elasticsearch, Logstash, Kibana)
Pros:
Active community and frequent updates
Scalable for small to large environments
Free and open-source; paid cloud version available
Ideal For: Enterprises needing a flexible, scalable SIEM with strong compliance features.
money bag Cost

Wazuh SIEM is completely free to use. It is an open-source security platform that integrates SIEM and XDRcapabilities, and it comes with:
No licensing cost
No vendor lock-in
Free community support
You can deploy it on-premises, in virtualized environments, containers, or in the cloud. While the core platform is free, you may incur costs for infrastructure (e.g., VMs, storage, bandwidth) and optional managed services like Wazuh Cloud, which offers a hosted version with additional support and scalability features.
rocket Deployment Guide

Here’s a deployment guide for integrating Wazuh with Cribl Stream, tailored for a sandbox or production-ready environment. This assumes you're using Docker or VMs and want to route Wazuh alerts/logs into Cribl for enrichment, filtering, and forwarding.
🛠️ Wazuh + Cribl Deployment Guide

1. Prerequisites

Wazuh Manager (can be deployed via Docker or VM)
Cribl Stream (Docker, VM, or Cribl Cloud)
Elastic Stack (optional, for Wazuh dashboards)
Syslog or Filebeat (for log forwarding)
2. Deploy Wazuh (Docker-based)

Use the official Wazuh Docker Compose setup:



git clone https://github.com/wazuh/wazuh-docker.git
cd wazuh-docker
docker-compose -f single-node.yml up -d
This sets up:
Wazuh Manager
Filebeat
Elasticsearch
Kibana
3. Deploy Cribl Stream

You can deploy Cribl Stream via Docker:



docker run -d \
  --name cribl \
  -p 9000:9000 \
  -v /opt/cribl:/opt/cribl \
  cribl/cribl:latest
Or use Cribl Cloud if preferred.
4. Configure Wazuh to Forward Logs to Cribl

Option A: Syslog Output

Edit /var/ossec/etc/ossec.conf on the Wazuh Manager:



<output>
  <syslog>
    <server>cribl-host-ip</server>
    <port>514</port>
    <format>json</format>
  </syslog>
</output>
Restart Wazuh Manager:



systemctl restart wazuh-manager
Option B: Filebeat to Cribl

Edit Filebeat output in /etc/filebeat/filebeat.yml:



output.logstash:
  hosts: ["cribl-host-ip:5044"]
Or use Cribl’s HTTP input if preferred.
5. Configure Cribl Inputs

In Cribl UI:
Go to Data > Inputs
Add a Syslog or Beats input
Set the port to match Wazuh output (e.g., 514 or 5044)
6. Create a Pipeline in Cribl

Go to Pipelines
Create a new pipeline (e.g., wazuh_pipeline)
Add functions like:
Parser (JSON)
Eval (for enrichment)
Filter (to drop noise)
Route to desired destinations (e.g., Splunk, Elastic, S3)
7. Validate Integration

Generate a test alert in Wazuh
Confirm it appears in Cribl’s Live Data
Check routing and enrichment
✅ Optional Enhancements

Add GeoIP or threat intel enrichment