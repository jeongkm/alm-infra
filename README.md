# Ansible을 이용한 Application Lifecicle Management 핸즈온 랩 환경 구성

이 문서는 Ricardo Zanini의 [블로그](https://www.redhat.com/en/blog/integrating-ansible-jenkins-cicd-process)와 [GitHub 소스](https://github.com/ricardozanini/vagrant-alm)를 기반으로 VM 서버에 ALM 랩 환경을 구축하는 과정을 안내합니다.

## 사전 준비 - VM

- VM 용도 : 호스트이름, OS, IP
- Ansible : ansible-1, Ubuntu 18.04, 172.16.167.137
- Jenkins : jenkins-1, CentOS 7.7, 172.16.177.106
- SonarQube : sonar-1, CentOS 7.7, 172.16.186.111
- Nexus OSS : nexus-1, CentOS 7.7, 172.16.177.214
- App Server : appserver-1, CentOS 7.7, 172.16.190.73


## 


## 진행

1. Ansible 서버에 SSH 접속

2. Ansible VM에 Ansible 설치

apt-get update
apt-get install apt-transport-https wget gnupg
apt-add-repository ppa:ansible/ansible
apt-get update
apt-get install ansible
ansible --version

vi /etc/ansible/ansible.conf

  71 host_key_checking = False


3. Ansible Playbook 다운로드

git clone https://github.com/jeongkm/alm-infra.git


4. 인벤토리 파일 변경 : 각 서버의 IP, SSH 접근 가능하고 SUDO 권한을 가진 ID와 Password 입력

cd alm-infra/ansible
vi inventory.ini

[jenkins_server]
jenkins-1 ansible_ssh_host=172.16.177.106 ansible_ssh_port=22 ansible_user=root ansible_password=Vkdldj1!

[sonar_server]
sonar-1 ansible_ssh_host=172.16.186.111 ansible_ssh_port=22 ansible_user=root ansible_password=Vkdldj1!

[nexus_server]
nexus-1 ansible_ssh_host=172.16.17.214 ansible_ssh_port=22 ansible_user=root ansible_password=Vkdldj1!

[app_server]
appserver-1 ansible_ssh_host=172.16.190.73 ansible_ssh_port=22 ansible_user=root ansible_password=Vkdldj1!

5. Nexus 서버의 URL 입력
cd alm-infra/ansible/group_vars
vi nexus_server.yml

nexus_version: '3.24.0'
nexus_timezone: 'Asia/Seoul'
nexus_admin_password: 'admin123'
nexus_anonymous_access: true
public_hostname: '<nexus server URL>'

6. Playbook 검토

ansible-playbook --syntax-check alm.yml -i inventory.ini

ansible-playbook --list-hosts playbook.yml -i inventory.ini

ansible-playbook --check playbook.yml  -i inventory.ini





## 참고자료

- Integrating Ansible with Jenkins in a CI/CD process : https://www.redhat.com/en/blog/integrating-ansible-jenkins-cicd-process
- Vagrant ALM - Application Lifecicle Management for labs and tests : https://github.com/ricardozanini/vagrant-alm
- 우분투 리눅스에 Ansible 설치 : https://techexpert.tips/ko/%EC%96%B4%EC%8A%AC%EC%97%90-%EC%9D%B4%EC%8A%AC%EC%9D%B4-%EC%9E%88%EB%8A%94/%EC%9A%B0%EB%B6%84%ED%88%AC-%EB%A6%AC%EB%88%85%EC%8A%A4%EC%97%90-ansible-%EC%84%A4%EC%B9%98/
