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
-> Moved the original access file which includes Access Key info to a new file 

```
[ec2-user@ip-10-100-10-100 aws-scripts-mon]$ crontab -l
```

```
* * * * * ~/aws-scripts-mon/mon-put-instance-data.pl --mem-util --mem-used --mem-avail --disk-space-util --disk-path=/ --from-cron
```
-> Make sure that if the crontab is set up properly to monitor the server periodically 
-> Currently, the path where the monitoring tool is installed is well set 

*You can always edit or add diskpath EX): --disk-path=/cont01 -disk-path=/cont02

```
[ec2-user@ip-10-100-10-100 aws-scripts-mon]$ sudo service crond restart
```
-> Restart crontab service to activate it 

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

-> The execution command is a command that checks whether the installed monitoring tool works well, 
and if the result is as follows ("Successfully reported metrics to CloudWatch").
The installation was successful and you should check the metric values (memory, disk) in the CloudWatch dashboard 

* When you enter the above command and if the result is "EC2 has no IAM role", you must check the instance's IAM role in the EC2 dashboard

The method is as follows in AWS EC2 dashboard: 

1. After selecting the instance, right-click and select Modify IAM role in the security category


2. Save after granting the IAM role to the instance


3. Check if the IAM role has been properly granted to the instance

4. Check the result after re-entering the monitoring tool verify command
[ec2-user@ip-10-100-10-100 aws-scripts-mon]$ ./mon-put-instance-data.pl --mem-used-incl-cache-buff --mem-util --mem-used --mem-avail

-> If the value is as follows, it means that it has been successfully set. Check the Metric value on the CloudWatch dashboard again, and if the memory and disk values of the instance exist, it's completed now 

```
Successfully reported metrics to CloudWatch. Reference Id: 07297645-facc-4f35-9550-767c00a94d5e
```
