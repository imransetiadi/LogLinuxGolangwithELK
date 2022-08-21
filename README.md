<!-- ABOUT THE PROJECT -->
## About The Project

Monitoring Log Linux with RSYSLOG and ELK Stack. Sometimes you may want to monitor SSH intrusions on your VMs. Sometimes, you might want to see what errors were raised by your application server on a certain day, on a very specific hour. Or you may want to have some insights about who stopped your systemd service on one of your VMs. In this tutorial, we are to build a complete log monitoring pipeline using the ELK stack (ElasticSearch, Logstash and Kibana) and Rsyslog as a powerful syslog server.

<!-- GETTING STARTED -->
## Getting Started

Monitoring Linux logs is crucial and every DevOps engineer should know how to do it. Here’s why You have real-time visual feedback about your logs : probably one of the key aspects of log monitoring, you can build meaningful visualizations (such as datatables, pies, graphs or aggregated bar charts) to give some meaning to your logs. You are able to aggregate information to build advanced and more complex dashboards : sometimes raw information is not enough, you may want to join it with other logs or to compare it with other logs to identify a trend. A visualization platform with expression handling lets you perform that. You can quickly filter for a certain term, or given a certain time period  if you are only interested in SSH logs, you can build a targeted dashboard for it.

Historically, Linux logging starts with syslog. Syslog is a protocol developed in 1980 which aims at standardizing the way logs are formatted, not only for 
Linux, but for any system exchanging logs. From there, syslog servers were developed and were embedded with the capability of handling syslog messages. 

### Prerequisites

You have VM or Linux OS, and Internet Connection 

### Installation

1. Installing Java on Ubuntu
   ```sh
   sudo apt-get install default-jre
   ```
2. Adding Elastic packages to your instance
   ```sh
   wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
   ```
3. Then, you can add Elastic source to your APT source list file.
   ```sh
   echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
   
   cat /etc/apt/sources.list.d/elastic-7.x.list deb https://artifacts.elastic.co/packages/7.x/apt stable main
   
   sudo apt-get update
   ```
4. Installing ElasticSearch
   ```sh
   sudo apt-get install elasticsearch
   sudo systemctl start elasticsearch
   ● elasticsearch.service - Elasticsearch
   Loaded: loaded (/usr/lib/systemd/system/elasticsearch.service; disabled; vendor preset: enabled)
   Active: active (running) since Mon 2019-07-08 18:19:45 UTC; 2 days ago
     Docs: http://www.elastic.co
   ```
5. Watching which applications listen on a targeted port 
   ```sh
   sudo lsof -i -P -n | grep LISTEN | grep 9200
   java      10667   elasticsearch  212u  IPv6 1159208890      0t0  TCP [::1]:9200 (LISTEN)
   java      10667   elasticsearch  213u  IPv6 1159208891      0t0  TCP 127.0.0.1:9200 (LISTEN)
   ```
6. Executing a simple ElasticSearch query
   ```sh
   curl -XGET 'http://localhost:9200/_all/_search?q=*&pretty'
   ```
7. Installing Logstash
   ```sh
   sudo apt-get install logstash
   ```
8. Again, a Logstash service will be created, and you need to activate it.
   ```sh
   sudo systemctl status logstash
   sudo systemctl start logstash
   ```
9. By default, Logstash listens for metrics on port 9600. As we did before, list the open ports on your computer looking for that specific port.
   ```sh
   sudo lsof -i -P -n | grep LISTEN | grep 9600
   java      28872        logstash   79u  IPv6 1160098941      0t0  TCP 127.0.0.1:9600 (LISTEN)
   ```
10. Installing Kibana
    ```sh
    sudo apt-get install kibana
    ```
11. As usual, start the service and verify that it is working properly.
    ```sh
    sudo systemctl start kibana
    sudo lsof -i -P -n | grep LISTEN | grep 5601
    node       7253          kibana   18u  IPv4 1159451844      0t0  TCP *:5601 (LISTEN)
    ```
    Head over to http://localhost:5601 with your browser 
   
12. Routing Linux Logs to ElasticSearch
    To create Logstash configuration files, head over to /etc/logstash/conf.d and create a logstash.conf file.
    Inside, append the following content:
    ```sh
    input {                                                                                      
     udp {                                                                                      
       host => "127.0.0.1"                                                                      
       port => 10514                                                                            
       codec => "json"                                                                          
       type => "rsyslog"                                                                        
     }                                                                                          
    }
    
    filter { }
    
    output {                                                                                     
      if [type] == "rsyslog" {                                                                   
        elasticsearch {                                                                          
           hosts => [ "127.0.0.1:9200" ]                                                          
        }                                                                                        
      }                                                                                          
    }      
    ```
    Note : for this tutorial, we are using the UDP input for Logstash, but if you are looking for a more reliable way to transfer your logs, you should      probably use the TCP input. The format is pretty much the same, just change the UDP line to TCP.

13. Restart your Logstash service.
    ```sh
    sudo systemctl restart logstash
    ```
14. Installing Logstash
    ```sh
    sudo apt-get install logstash
    ```
15. To verify that everything is running correctly, issue the following command:
    ```sh
    netstat -na | grep 10514
    udp        0      0 127.0.0.1:10514         0.0.0.0:* 
    ```
16. In order to forward logs in rsyslog, head over to /etc/rsyslog.d and create a new file named 70-output.conf
    Inside your file, write the following content:
    ```sh
    # This line sends all lines to defined IP address at port 10514
    # using the json-template format.

    *.*                         @127.0.0.1:10514;json-template
    ```
17. Now that you have log forwarding, create a 01-json-template.conf file in the same folder, and paste the following content:
    ```sh
    template(name="json-template"
      type="list") {
        constant(value="{")
        constant(value="\"@timestamp\":\"")     property(name="timereported" dateFormat="rfc3339")
        constant(value="\",\"@version\":\"1")
        constant(value="\",\"message\":\"")     property(name="msg" format="json")
        constant(value="\",\"sysloghost\":\"")  property(name="hostname")
        constant(value="\",\"severity\":\"")    property(name="syslogseverity-text")
        constant(value="\",\"facility\":\"")    property(name="syslogfacility-text")
        constant(value="\",\"programname\":\"") property(name="programname")
        constant(value="\",\"procid\":\"")      property(name="procid")
        constant(value="\"}\n")
    }
    ```
18. As you probably guessed it, for every incoming message, rsyslog will interpolate log properties into a JSON formatted message, and forward it to      Logstash, listening on port 10514. Restart your rsyslog service, and verify that logs are correctly forwarded to ElasticSearch.
    ```sh
    sudo systemctl restart rsyslog
    curl -XGET 'http://localhost:9200/logstash-*/_search?q=*&pretty'
    {
     "took": 2,
     "timed_out": false,
     "_shards": {
     "total": 1,
     "successful": 1,
     "skipped": 0,
     "failed": 0
    },
     "hits": {
     "total": {
     "value": 10000,
     "relation": "gte"
    },
     "max_score": 1,
     "hits": [
      {
        "_index": "logstash-2019.07.08-000001",
        "_type": "_doc",
        "_id": "GEBK1WsBQwXNQFYwP8D_",
        "_score": 1,
        "_source": {
          "host": "127.0.0.1",
          "severity": "info",
          "programname": "memory_usage",
          "facility": "user",
          "@timestamp": "2019-07-09T05:52:21.402Z",
          "sysloghost": "schkn-ubuntu",
          "message": "                                  Dload  Upload   Total   Spent    Left  Speed",
          "@version": "1",
          "procid": "16780",
          "type": "rsyslog"
         }
        }
       ]
      }
     }                
    ```
Building a Log Dashboard in Kibana

Head over to Kibana (on http://localhost:5601) and visit menu Discover to see Log Linux. 

<!-- Conclusion -->
## Conclusion

With this tutorial, We know have a better understanding of how i can monitor i entire logging infrastructure easily with Rsyslog and the ELK stack.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

