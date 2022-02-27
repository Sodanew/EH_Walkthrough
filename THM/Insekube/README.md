# Reconnaissance

## Network Scanning

### Port 22
![22 Nmap result](img/ssh-nmap.png)  
Discover port 22 with OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)  

### Port 80
![80 Nmap result](img/http-nmap.png)  
Discover port 80 is opened. Nmap script dint show any version or banner info about the services.  

## Website Enumeration

### Ping Command Output
![ping output](img/ping.png)  
Insert localhost as the input in the provided field. We can see that a ping cmd is execute as the output show. We can try bypass it by inject '|' into it.  

### List Directory
![ls -la output](img/cmd_flaw.png)  
Discovered command injection flaw. We can see that the ls cmd is executed success and the backend system show the current directory.  

# Initial Foothold
![shell gained](img/rev_sh.png)  
We get revese shell gain into the boxes.  

## Environmental Flag
![flag](img/flag1.png)  
Since we get shell, now we can get the flag in the environment variable as the hint given.  

## Kubectl Enumeration

> **kubectl** is a API that can use to interact with the cluster.

### List Pods
![pod_error](img/list-pod-error.png)  
We try to list pods. But we get error and tell us that current service account user don’t have related privileges.  

### Privileges Right
![right](img/secrets.png)  
Check permissions. Discover that we can list secrets resources.  

### List Secret
![list_secret](img/secrets_flag.png)  
List all kubernetes secretes. Discover secretflag.  

### Get Secret
![flag](img/flag2.png)  
Read specific secret and obtain flag2  

### Grafana Services
![grafana_services](img/grafana.png)  
List environment variables to check for kubernetes related services. We discover Grafana service running in the server.  
>By default Kubernetes creates environment variables containing the host and port of the other services running in the cluster  
Also we can discover the Grafana host IP is in 10.108.133.228.  

## Grafana Enumeration

### Login Page
![login](img/login.png)  
Curl the IP with the port and discover the login page and the source code in HTML format.  

### Grafana Version
![version](img/grafana_version.png)  
Discover grafana version. Found the [CVE](https://github.com/julesbozouklian/CVE-2021-43798) for this version.  

### Passwd File
![etc_passwd](img/etc_passwd.png)  
Manually do the POC exploit and try to grab /etc/passwd file. We successful grab the file.  

## Kubernetes Services Token Enumeration

### Services Account Token
![token](img/token.png)  
> Kubernetes stores the token of the service account running a pod in /var/run/secrets/kubernetes.io/serviceaccount/token 
Obtain the token from the grafana pods  

### Token Privileges
![token_priv](img/token_privileges.png)  
Check the TOKEN service account privileges. Discover that the token's service account can do all the verbs in all the resources. Which mean this is a 'cluster-admin' service account.  

### List pods
![list_pods](img/list-pods.png)  
List all the pods again. We discover the node contain 2 pod are running. Please note that 'syringe-79b66d66d7-7mxhd' is current pod we are in via the reverse shell.  

### Shell in Grafana pod
![shell_grafana](img/shell_grafana_pod.png)  
Get shell into the Grafana pod.  

### Grafana pod Environment Variables
![flag3](img/flag3.png)  
Obtain flag on Grafana pod  

## Escape Node

### Evil Yaml
![evil-yaml](img/evil-yaml.png)  
Transfer the bad pod config yaml file into the syringe pod  

### Create Evil Pod
![evil-pod](img/evil-pod.png)  
Create the evil-pod with the evil-yaml.  

### Check created pod
![soda-pod](img/soda-pod.png)  
Check the pod is being created  

# Privilege Escalation

## Shell in evil-pod
![shell_evil_pod](img/shell_evil_pod.png)  
Get shell into the fully privileges evil-pod.  

## Root flag
![root-flag](img/flag4.png)  
Obtain the last flag  