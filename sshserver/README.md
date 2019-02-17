# SSHSERVER

## Parveen ASIX M06 2018-2019

## Description:

Practica with ldap,pam_host and sshserver.Main to config. of ssh server. 

### important steps:


Make sshnet network to put togather from exemple name by (sshnet)
now we need add all docker in this network

By using this command

```
[root@localhost pam_ssh_ldap]# docker network create sshnet
8ff156696dccf766367d5fbb6814b46e2f4b286dd4ea73d980fbfd67a943671e
```

## 1 Ldap Server
	* Dockerfile need to edit some pacquetes  (procps openldap-clients openldap-servers)
	* Put database edt.org also new group and manager as admin
	* Turn on server slpad	
	This one my server from use
	
```
docker run --rm --network sshnet --name ldap --hostname ldap -d parveen1992/ldap
```

** check this by correct ip address my ip is 172.19.0.2 **

```
[root@localhost pam_ssh_ldap]# docker ps 
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
c10688f5301c        parveen1992/ldap    "/opt/docker/start..."   9 seconds ago       Up 7 seconds        389/tcp             ldap

[root@localhost practica]# ldapsearch -x -LLL -h 172.19.0.2 -b dc=edt,dc=org 'ou=grups'
	dn: ou=grups,dc=edt,dc=org
	ou: groups
	ou: grups
	description: Container per a grups
	objectClass: organizationalunit
```

## 2 ssh Server
	
	* Docker line need add more packets  (procps vim passwd openldap-clients nss-pam-ldapd authconfig pam_mount openssh-server nmap).
	* Make some localuser and group late for check test.also need to give password.
	* Now we need cp file accordig to setting in (/opt/docker/auth.sh)to connect ldapserver 	
	* File pam need to conf.(/etc/pam.d/sshd)	
	* Now we need connection to ldap server befor we make (Ldap server).
	* Start server to connect ldap server(nscd,nslcd).
	* After connect server we need edit ssh give conf of user(/etc/security/access-sshd.conf).
	* mkdir for every user and import give own premission.
	* now start server (smbd,nmbd).
	This one my server to user
	
```
docker run --rm -p 1022:1022 --network sshnet --name sshserver --hostname sshserver -it parveen1992/sshserver
```

** check this **
	
```
[root@sshserver docker]# getent passwd pere
pere:*:5001:100:Pere Pou:/tmp/home/pere:

[root@sshserver docker]# ssh pau@localhost -p 1022
The authenticity of host '[localhost]:1022 ([::1]:1022)' can't be established.
ECDSA key fingerprint is SHA256:N5DA17YiIlsnkQtVpabsz6872+FKYk3FKo483uJObAw.
ECDSA key fingerprint is MD5:1d:06:0e:27:42:06:37:b7:fb:67:95:c8:98:d9:6e:d6.
Are you sure you want to continue connecting (yes/no)? y
Please type 'yes' or 'no': yes
Warning: Permanently added '[localhost]:1022' (ECDSA) to the list of known hosts.
pau@localhost's password: 
Creating directory '/tmp/home/pau'.
[pau@sshserver ~]$ pwd
/tmp/home/pau
[pau@sshserver ~]$ ll
total 0

[pau@sshserver ~]$ ssh marta@localhost -p 1022
The authenticity of host '[localhost]:1022 ([::1]:1022)' can't be established.
ECDSA key fingerprint is SHA256:N5DA17YiIlsnkQtVpabsz6872+FKYk3FKo483uJObAw.
ECDSA key fingerprint is MD5:1d:06:0e:27:42:06:37:b7:fb:67:95:c8:98:d9:6e:d6.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[localhost]:1022' (ECDSA) to the list of known hosts.
marta@localhost's password: 
Permission denied, please try again.
marta@localhost's password: 
Permission denied, please try again.
marta@localhost's password: 
marta@localhost: Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).
```
Try from outside of container nust check ip adderess

```
[root@localhost pam_ssh_ldap]# ssh pau@172.19.0.3 -p 1022
pau@172.19.0.3's password: 
Last login: Sun Feb 17 15:17:39 2019 from ::1
[pau@sshserver ~]$ 
[pau@sshserver ~]$ pwd
/tmp/home/pau
```


## 3 Pamhost or cliente
	
	* Docker line need add more packets (procps passwd openldap-clients nss-pam-ldapd authconfig pam_mount openssh-server)
	* ssh-key to connect server and user (ssh-keygen -A).
	* Make confgure file for ldapserver connection (nsswitch.conf).
	* ALso need pam system conf. for login user and mkhomedir (/etc/pam.d/system-auth.edt) .
	* Start server to connect ldap server(nscd,nslcd).
	This my PamHost is
	
```
docker run --rm --network sshnet --privileged --name client  --hostname client -it parveen1992/hostmountssh
```

** check this ** 
	
```
[root@client docker]# getent passwd pere
pere:*:5001:100:Pere Pou:/tmp/home/pere:

[root@client docker]#  ssh marta@sshserver -p 1022
The authenticity of host '[sshserver]:1022 ([172.19.0.3]:1022)' can't be established.
ECDSA key fingerprint is SHA256:N5DA17YiIlsnkQtVpabsz6872+FKYk3FKo483uJObAw.
ECDSA key fingerprint is MD5:1d:06:0e:27:42:06:37:b7:fb:67:95:c8:98:d9:6e:d6.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[sshserver]:1022,[172.19.0.3]:1022' (ECDSA) to the list of known hosts.
marta@sshserver's password: 
Permission denied, please try again.
marta@sshserver's password: 
Permission denied, please try again.
marta@sshserver's password: 
marta@sshserver: Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).

[root@client docker]#  ssh pere@sshserver -p 1022
pere@sshserver's password: 
Connection closed by 172.19.0.3 port 1022

[root@client docker]#  ssh pau@sshserver -p 1022
pau@sshserver's password: 
Last login: Sun Feb 17 15:21:24 2019 from 172.19.0.1

[pau@sshserver ~]$ pwd
/tmp/home/pau


```	



### Github repo. with all of images file we need conf.(or your castom conf.). 

[Github Parveen](https://github.com/parveen1/sshdldappam)


### Dockerhub repo. links

[Docker Parveen ldap](https://hub.docker.com/r/parveen1992/ldap)

[Docker parveen sshserver](https://hub.docker.com/r/parveen1992/sshserver)

[Docker Parveen hostmountssh](https://hub.docker.com/r/parveen1992/hostmountssh)

### All to start

```
docker run --rm --network sshnet --name ldap --hostname ldap -d parveeen1992/ldap
docker run --rm -p 1022:1022 --network sshnet --name sshserver --hostname sshserver -it parveen1992/sshserver
docker run --rm --network sshnet --privileged --name client  -hostname client -it parveen1992/hostmountssh
```

### Check this order after strat to verify

```
	[pere@client ~]$ su - anna
	pam_mount password:
	Creating directory '/tmp/home/anna'.
	[anna@client ~]$ pwd
	/tmp/home/anna
```
