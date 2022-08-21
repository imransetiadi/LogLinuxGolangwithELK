<!-- ABOUT THE PROJECT -->
## About The Project

There are many great README templates available on GitHub; however, I didn't find one that really suited my needs so I created this enhanced one. I want to create a README template so amazing that it'll be the last one you ever need -- I think this is it.

Here's why:
* Your time should be focused on creating something amazing. A project that solves a problem and helps others
* You shouldn't be doing the same tasks over and over like creating a README from scratch
* You should implement DRY principles to the rest of your life :smile:

Of course, no one template will serve all projects since your needs may be different. So I'll be adding more in the near future. You may also suggest changes by forking this repo and creating a pull request or opening an issue. Thanks to all the people have contributed to expanding this template!

Use the `BLANK_README.md` to get started.


<!-- GETTING STARTED -->
## Getting Started

This is an example of how you may give instructions on setting up your project locally.
To get a local copy up and running follow these simple example steps.

### Prerequisites

This is an example of how to list things you need to use the software and how to install them.
* npm
  ```sh
  npm install npm@latest -g
  ```

### Installation

_Below is an example of how you can instruct your audience on installing and setting up your app. This template doesn't rely on any external dependencies or services._

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
9. Install NPM packages
   ```sh
   npm install
   ```
10. Install NPM packages
    ```sh
    npm install
    ```
11. Installing Logstash
    ```sh
    sudo apt-get install logstash
    ```
12. By default, Logstash listens for metrics on port 9600. As we did before, list the open ports on your computer looking for that specific port.
    ```sh
    sudo lsof -i -P -n | grep LISTEN | grep 9600
    java      28872        logstash   79u  IPv6 1160098941      0t0  TCP 127.0.0.1:9600 (LISTEN)
    ```
13. Installing Kibana
    ```sh
    sudo apt-get install kibana
    ```
14. As usual, start the service and verify that it is working properly.
    ```sh
    sudo systemctl start kibana
    sudo lsof -i -P -n | grep LISTEN | grep 5601
    node       7253          kibana   18u  IPv4 1159451844      0t0  TCP *:5601 (LISTEN)
    ```
    Head over to http://localhost:5601 with your browser 
   
15. Routing Linux Logs to ElasticSearch
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

16. Restart your Logstash service.
    ```sh
    sudo systemctl restart logstash
    ```
17. Installing Logstash
    ```sh
    sudo apt-get install logstash
    ```
18. To verify that everything is running correctly, issue the following command:
    ```sh
    netstat -na | grep 10514
    udp        0      0 127.0.0.1:10514         0.0.0.0:* 
    ```
19. In order to forward logs in rsyslog, head over to /etc/rsyslog.d and create a new file named 70-output.conf
    Inside your file, write the following content:
    ```sh
    npm install
    ```
11. Installing Logstash
   ```sh
   sudo apt-get install logstash
   ```
<p align="right">(<a href="#readme-top">back to top</a>)</p>


<!-- CONTACT -->
## Contact

Imran Setiadi - - imransetiadi22@gmail.com

<p align="right">(<a href="#readme-top">back to top</a>)</p>

