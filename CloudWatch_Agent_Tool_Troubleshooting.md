# How to troubleshoot CloudWatch Agent Tool in AWS #

* This document is for troubleshooting some issues which is related to being unable to see metric values about some instances after installing CloudWatch Agent tool in AWS

```
[BASTION:ec2-user@ip-10-x-x-x ~]$ 
-> Connect to the server which isnt working through a bastion server 

[BASTION:ec2-user@ip-10-100-10-14 ~]$ ssh -i key-renaultsamsungm.pem ec2-user@10.100.10.100
-> Connect to the server with the key.pem file 
```

```
[ec2-user@ip-10-100-10-100 ~]$ ll
total 281072
drwxrwxr-x 2 ec2-user ec2-user      4096 Nov 28 15:44 aws-scripts-mon
```
-> Monitoring tool has been installed properly 

```
[ec2-user@ip-10-100-10-100 ~]$ cd aws-scripts-mon/
```
-> Move to the directory which has the CloudWatch Agent tool 

```
[ec2-user@ip-10-100-10-100 aws-scripts-mon]$ ll
total 96
-r--r--r-- 1 ec2-user ec2-user 17021 Mar  6  2015 AwsSignatureV4.pm
-r--r--r-- 1 ec2-user ec2-user 22487 Mar  6  2015 CloudWatchClient.pm
-rw-r--r-- 1 ec2-user ec2-user  9124 Mar  6  2015 LICENSE.txt
-rw-r--r-- 1 ec2-user ec2-user   138 Mar  6  2015 NOTICE.txt
-rw-r--r-- 1 ec2-user ec2-user    89 Nov 28 15:44 awscreds.template
-rwxr-xr-x 1 ec2-user ec2-user  9739 Mar  6  2015 mon-get-instance-stats.pl
-rwxr-xr-x 1 ec2-user ec2-user 18144 Mar  6  2015 mon-put-instance-data.pl
```
-> There are some files which are related to the CloudWatch Agent tool installation

-> The current problematic part is that the CloudWatch Agent does not need a personal AccessKey 
but just needs to set the specific IAM role to the EC2 instance, so it is necessary to move the files containing the current key file to somewhere 
which doesn’t affect on the agent tool on the server 

```
[ec2-user@ip-10-100-10-100 aws-scripts-mon]$ mv awscreds.conf old_awscreds.conf
```
-> 기존에 키를 포함하고 있는 access 파일을 다른 곳으로 옮겨줌 

```
[ec2-user@ip-10-100-10-100 aws-scripts-mon]$ crontab -l
```
* * * * * ~/aws-scripts-mon/mon-put-instance-data.pl --mem-util --mem-used --mem-avail --disk-space-util --disk-path=/ --from-cron

-> 주기적으로 모니터링 역할을 할 수 있도록 crontab이 잘 설정되어있는지 확인함 
-> 현재 모니터링툴이 설치되어있는 경로나 모니터링을 해야하는 위치가 잘 설정되어 있음

*diskpath는 수정 혹은 추가할 수 있음. EX): --disk-path=/cont01 -disk-path=/cont02

```
[ec2-user@ip-10-100-10-100 aws-scripts-mon]$ sudo service crond restart
```
-> crontab 서비스를 재시작하여 서비스를 활성화 시켜줌 

```
[ec2-user@ip-10-100-10-100 aws-scripts-mon]$ ./mon-put-instance-data.pl --mem-used-incl-cache-buff --mem-util --mem-used --mem-avail
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
	LANGUAGE = (unset),
	LC_ALL = (unset),
	LC_CTYPE = "UTF-8",
	LANG = "en_US.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to the standard locale ("C").

Successfully reported metrics to CloudWatch. Reference Id: 07297645-facc-4f35-9550-767c00a94d5e
```

-> 해당 실행 명령어는 설치된 모니터링 툴이 잘 작동하는지를 확인하는 명령어로 결과가 다음과 같다면('Successfully reported metrics to CloudWatch') 
성공적으로 설치가 된 것으로 CloudWatch 대시보드에서 메트릭값(메모리, 디스크)을 확인해야함 

*위의 명령어를 입력하였는데 결과가 'EC2 has no IAM role' 이라면 EC2 대시보드에서 인스턴스의 IAM role을 설정해주면 됨.
방법은 아래와 같음 

1. 해당 인스턴스를 선택 후 오른쪽 마우스 클릭으로 보안 항목에서 IAM role 수정하기를 선택


2. 인스턴스에 원하는 IAM role을 부여해준 후 저장


3. 인스턴스에 IAM role이 잘 부여됐는지 확인


4. 모니터링 툴 verify 명령어 다시 입력 후 결과 확인 
[ec2-user@ip-10-100-10-100 aws-scripts-mon]$ ./mon-put-instance-data.pl --mem-used-incl-cache-buff --mem-util --mem-used --mem-avail

-> 값이 아래와 같으면 성공적으로 설정이 되었다는 의미로 CloudWatch 대시보드의 Metric 값을 다시 한번 확인하고 해당 인스턴스의 memory 및 disk 값이 존재한다면 완료
