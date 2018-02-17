# pksecuautomationansible2

###
```
- name: installing jenkins in ubuntu 16.04
  hosts: "192.168.1.7"
  remote_user: ubuntu
  gather_facts: False
  become: True

tasks:
  - name: install python 2
    raw: test -e /usr/bin/python || (apt -y update && apt install -y python-minimal)

  - name: install curl and git
    apt: name={{ item }} state=present update_cache=yes
    with_items:
      - curl
      - git
  - name: adding jenkins gpg key    
    apt_key:      
      url: https://pkg.jenkins.io/debian/jenkins-ci.org.key      
      state: present  
  - name: jeknins repository to system    
    apt_repository:      
      repo: http://pkg.jenkins.io/debian-stable binary/      
      state: present 
  - name: installing jenkins    
    apt:      
      name: jenkins      
      state: present      
      update_cache: yes  
  - name: adding jenkins to startup    
    service:      
      name: jenkins      
      state: started      
      enabled: yes  
  - name: printing jenkins default administration password    
    command: cat /var/lib/jenkins/secrets/initialAdminPassword    
    register: jenkins_default_admin_password    
  - debug:      
      msg: "{{ jenkins_default_admin_password.stdout }}"
```


## Setting Up a Hardened WordPress with Encrypted Automated Backups

### Setting up nginx web server
```
- name: adding nginx signing key
  apt_key:
    url: http://nginx.org/keys/nginx_signing.key
    state: present

- name: adding sources.list deb url for nginx
  lineinfile:
    dest: /etc/apt/sources.list
    line: "deb http://nginx.org/packages/mainline/ubuntu/ trusty nginx"

- name: update the cache and install nginx server
  apt:
    name: nginx
    update_cache: yes
    state: present

- name: updating customized templates for nginx configuration
  template:
    src: "{{ item.src }}"
    dest: "{{ item.dst }}"

  with_items:
    - { src: "templates/defautlt.conf.j2", dst: "/etc/nginx/conf.d/default.conf" }    
  
  notify
    - start nginx
    - startup nginx
```
### Setting up MySQL database
```
- name: create WordPress database    
  mysql_db:      
    name: "{{ WordPress_database_name }}"      
    state: present      
    login_user: root      
    login_password: "{{ mysql_root_password }}"
- name: create WordPress database user    
  mysql_user:      
    name: "{{ WordPress_database_username }}"      
    password: "{{ WordPress_database_password }}"      
    priv: '"{{ WordPress_database_name }}".*:ALL'      
    state: present      
    login_user: root      
    login_password: "{{ mysql_root_password }}"
```










### Prerequisites for setting up Elastic Stack
```
- name: install python 2
  raw: test -e /usr/bin/python || (apt -y update && apt install -y python-minimal)

- name: accepting oracle java license agreement
  debconf:
    name: 'oracle-java8-installer'
    question: 'shared/accepted-oracle-license-v1-1'
    value: 'true'
    vtype: 'select'

- name: adding ppa repo for oracle java by webupd8team
  apt_repository:
    repo: 'ppa:webupd8team/java'
    state: present
    update_cache: yes

- name: installing java nginx apache2-utils and git
  apt:
    name: "{{ item }}"
    state: present
    update_cache: yes

  with_items:
    - python-software-properties
    - oracle-java8-installer
    - nginx
    - apache2-utils
    - python-pip
    - python-passlib
```
### Install ElasticSearch
```
- name: adding elastic gpg key for elasticsearch  
  apt_key:    
    url: "https://artifacts.elastic.co/GPG-KEY-elasticsearch"    
    state: present
- name: adding the elastic repository  
  apt_repository:    
    repo: "deb https://artifacts.elastic.co/packages/5.x/apt stable main"    
    state: present
- name: installing elasticsearch  
  apt:    
    name: "{{ item }}"    
    state: present    
    update_cache: yes  
  with_items:    
  - elasticsearch
- name: adding elasticsearch to the startup programs  
  service:    
    name: elasticsearch    
    enabled: yes    
    notify:    
      - start elasticsearch
- name: creating elasticsearch backup repo directory at {{ elasticsearch_backups_repo_path }}  
  file:    
    path: "{{ elasticsearch_backups_repo_path }}"    
    state: directory    
    mode: 0755    
    owner: elasticsearch    
    group: elasticsearch
- name: configuring elasticsearch.yml file  
  template:    
    src: "{{ item.src }}"    
    dest: /etc/elasticsearch/"{{ item.dst }}"  
  with_items:    
  - { src: 'elasticsearch.yml.j2', dst: 'elasticsearch.yml' }    
  - { src: 'jvm.options.j2', dst: 'jvm.options' }  
  notify:    
    - restart elasticsearch
- name: start elasticsearch  
  service:    
    name: elasticsearch    
    state: started
- name: restart elasticsearch  
  service:    
    name: elasticsearch    
    state: restarted
```
