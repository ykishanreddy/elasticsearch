# elasticsearch

Elasticsearch is one of the part of elastic stack, it is a distributed RESTful search engine which stores all of the collected data.

I have lanched ubunto EC2 instance in AWS and 
- ansible
- installed java8 
- installed elasticsearch 
- modify the configuration files.

And finally access with curl –X GET “localhost:9200”

---
- hosts: localhost
  tasks:
  - name: install openjdk8  ## install java8, which is running from java
    apt: update_cache=yes
    apt: name=openjdk-8-jdk state=present

  - name: import es pgp key  ##To secure system package install pgp key
    raw: wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -

  - name: install apt repo
    apt: name=apt-transport-https state=present

  - name: add es repo   ### Add ES repo for latest elasticsearch packages
    raw: echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list

  - name: install es  ## install ES
    apt: update_cache=yes ache_valid_time=3600
    apt: name=elasticsearch state=present

  - name: Update elasticsearch configuration file 
    replace:
      dest: "/etc/elasticsearch/elasticsearch.yml"
      regexp: "{{ item.regexp }}"
      replace: "{{ item.replace }}"
    with_items:
      - { regexp: '#network.host: 192.168.0.1', replace: 'network.host: localhost' }  ## you can replace with IP which you want access
      - { regexp: '#http.port: 9200', replace: 'http.port: 9200' }  ## U can replace with your custom IP, 9200 is default port No for every one knows
    become: yes

  - name: Ensure ES is running (and enable it at boot)
    service: name=elasticsearch state=started enabled=yes
    
    
    
    
I can able to install cross platform with both Ubunto and Rhel(centos) packages.
And will able lanch the cluster also
