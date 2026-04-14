#### E - Elasticsearch 
Database used to store logs.
Built by Elastic
Uses its own beginner-friendly language called ESQL (Elastic Search Query Language)

#### L - Logstash
A pipeline that collects telemetry from various sources
Filters and outputs that data into an Elasticsearch instance

##### Two ways of collecting telemetry data

- Beats
6 different types (File, Metric Packet, Winlog, Audit, Heartbeat)
To collect a type of data from an endpoint, download the specific *beat* to that endpoint

- Elastic Agents
Collects all different types of data using one unified agent per host ( What will be used in this challenge)


#### K - Kibana

Web console for visualizing telemetry from Logstash and Elasticsearch. 

##### Essentially, the ELK stack is similar to Splunk.

**Elasticsearch** = Indexer /  Search Head
**Logstash** = Heavy Forwarder
**Kibana** = Web GUI
**Beats/Agents** = Universal Forwarders

##### Why use ELK and not just Splunk?

1. Centralized logging
	1. Meets compliance requirements and sift through data in an incident
2. Flexibility
	1. Customize what data is ingested from the endpoints
3. Visualizations
	1. Observer info at a glance
	2. Gives stakeholders a quick graph as to what is happening
4. Scalability
	1. Easy to configure for larger environments
5. Ecosystem
	1. Many integrations and rich community
	2. You could ask the community to create an integration custom for your situation should the need arise
6. Many SIEMs are built on the ELK stack
	1. This gives a good foundation to work with other SIEMs
	2. Get comfortable using ELK, get comfortable using SIEMs other than Splunk