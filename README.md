# Ansible을 이용한 Application Lifecicle Management 핸즈온 랩 환경 구성



이 문서는 Ricardo Zanini의 [블로그](https://www.redhat.com/en/blog/integrating-ansible-jenkins-cicd-process)와 [GitHub 소스](https://github.com/ricardozanini/vagrant-alm)를 기반으로 하며, 프라이빗 네트워크의 VM 서버에 ALM 랩 환경을 구축하는 과정을 안내합니다.



## 사전 준비 - VM

이 문서에서 사용된 호스트이름, OS, IP는 예제입니다. 실습하시는 환경의 정보를 사용해주세요.

| VM 용도    | 호스트이름  | OS           | IP             | ID   | Password |
| ---------- | ----------- | ------------ | -------------- | ---- | -------- |
| Ansible    | ansible-1   | Ubuntu 18.04 | 172.16.167.137 | root | Passw0rd |
| Jenkins    | jenkins-1   | CentOS 7.7   | 172.16.177.106 | root | Passw0rd |
| SonarQube  | sonar-1     | CentOS 7.7   | 172.16.186.111 | root | Passw0rd |
| Nexus OSS  | nexus-1     | CentOS 7.7   | 172.16.177.214 | root | Passw0rd |
| App Server | appserver-1 | CentOS 7.7   | 172.16.190.73  | root | Passw0rd |

- 도메인 : alm.ibm.com




## Ansbile 및 각 ALM 서버 설정

1. Ansible VM에 SSH 접속

2. Ansible VM에 Ansible 설치

```bash
apt-get update
apt-get install apt-transport-https wget gnupg
apt-add-repository ppa:ansible/ansible
apt-get update
apt-get install ansible
ansible --version


vi /etc/ansible/ansible.conf

# 71라인 호스트 키 체킹 해제
host_key_checking = False
```



3. Ansible Playbook 다운로드

```bash
git clone https://github.com/jeongkm/alm-infra.git
```



4. 인벤토리 파일 변경 
   - 각 서버의 호스트네임과 IP, SSH 접근 가능한 ID와 Password 입력

```bash
cd alm-infra/ansible
vi inventory.ini

[jenkins_server]
jenkins-1 ansible_ssh_host=172.16.177.106 ansible_ssh_port=22 ansible_user=root ansible_password=passw0rd

[sonar_server]
sonar-1 ansible_ssh_host=172.16.186.111 ansible_ssh_port=22 ansible_user=root ansible_password=passw0rd

[nexus_server]
nexus-1 ansible_ssh_host=172.16.17.214 ansible_ssh_port=22 ansible_user=root ansible_password=passw0rd

[app_server]
appserver-1 ansible_ssh_host=172.16.190.73 ansible_ssh_port=22 ansible_user=root ansible_password=passw0rd
```



5. Nexus 서버의 FQDN 입력

```bash
cd alm-infra/ansible/group_vars
vi nexus_server.yml

nexus_version: '3.24.0'
nexus_timezone: 'Asia/Seoul'
nexus_admin_password: 'admin123'
nexus_anonymous_access: true
public_hostname: 'nexus-1.alm.ibm.com'
```



6. (선택 사항) Playbook 검토

```bash
cd alm-infra/ansible

ansible-galaxy install -r requirements.yml

ansible-playbook --syntax-check alm.yml -i inventory.ini

ansible-playbook --list-hosts alm.yml -i inventory.ini

ansible-playbook --check alm.yml  -i inventory.ini
```




7. playbook 실행

```bash
cd alm-infra/ansible
ansible-playbook alm.yml  -i inventory.ini
```



## ALM 서버 확인 



| 서버 용도 | URL                                | ID    | Password |
| --------- | ---------------------------------- | ----- | -------- |
| Jenkins   | http://jenkins-1.alm.ibm.com:8080/ | admin | admin123 |
| SonarQube | http://sonar-1.alm.ibm.com:9000/   | admin | Admin    |
| Nexus OSS | http://nexus-1.alm.ibm.com:8081/   | admin | admin123 |






## 참고자료

- Integrating Ansible with Jenkins in a CI/CD process : https://www.redhat.com/en/blog/integrating-ansible-jenkins-cicd-process
- Vagrant ALM - Application Lifecicle Management for labs and tests : https://github.com/ricardozanini/vagrant-alm
- 우분투 리눅스에 Ansible 설치 : https://techexpert.tips/ko/%EC%96%B4%EC%8A%AC%EC%97%90-%EC%9D%B4%EC%8A%AC%EC%9D%B4-%EC%9E%88%EB%8A%94/%EC%9A%B0%EB%B6%84%ED%88%AC-%EB%A6%AC%EB%88%85%EC%8A%A4%EC%97%90-ansible-%EC%84%A4%EC%B9%98/
