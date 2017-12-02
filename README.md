# Index-Oracle-to-Solr

This is a demo for how to use “DataImport” tool provided by Apache Solr to index data from Oracle DB.

# Infrastructure
For this demo, I’ve used two virtual machines on Google Cloud Platform:
1. Oracle XE – 1 machine of type n1-standard-1 (1 vCPU, 3.75 GB memory) and 30GB standard disk. OS is Ubuntu 16.04 and Oracle 11.2.0.2.0 XE instance. 
2. Solr cloud – 1 machine of type - n1-standard-4 (4 vCPUs, 15 GB memory) and 20GB SSD disk. The VM is a certified prebuilt Solr image from Bitnami. OS is Debian 8 and packages installed are  Apache 2.4.29, Java 1.8.0_151, OpenSSL 1.0.2m, and Apache Solr 7.1.0

# Oracle
