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
