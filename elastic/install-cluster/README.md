# Install Elasticsearch

We used pre-packaged "Elasticsearch Certified by Bitnami" VM on Google Compute Engine.

## Why use Bitnami Certified images?

Bitnami certifies that their images are secure, up-to-date, and packaged using industry best practices.
Bitnami monitors all components and libraries for vulnerabilities, outdated components, and application updates. When one is reported, they update and release every affected listing within a couple days at most.

## Version
6.0.0-0

## Operating system
Debian 8
Package contents
Elasticsearch 6.0.0
Apache 2.4.29
Java 1.8.0_151
OpenSSL 1.0.2m

# Install logstash 
  - wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
  - sudo apt-get install apt-transport-https
  - sudo apt-get update
  - sudo apt-get install apt-transport-https
  - echo "deb https://artifacts.elastic.co/packages/6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list
  - sudo ln -s /opt/bitnami/java/bin/java /usr/bin/java (if there is /usr/bin/java not found error)
  - sudo apt-get update && sudo apt-get install logstash
  - cd /usr/share/logstash
  - cd bin
  - sudo ./logstash-plugin install logstash-input-jdbc
  - wget http://download.oracle.com/otn/utilities_drivers/jdbc/11204/ojdbc6.jar
  - sudo mv ojdbc6.jar /usr/lib/


