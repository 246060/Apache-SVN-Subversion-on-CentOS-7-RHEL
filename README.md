# Apache-SVN-Subversion-on-CentOS-7-RHEL


## 1. Installing a Web Server


```bash
sudo -s
yum update
yum -y install httpd httpd-tools vim

firewall-cmd --permanent --zone=public --add-service=http
firewall-cmd --reload

systemctl start httpd
```

## 2. Installing Subversion

```bash
yum -y install subversion subversion-tools mod_dav_svn
```


## 3. Configuring Subversion

```bash
mkdir /svn
svnadmin create /svn/first-repo
chown -R apache:apache /svn/first-repo/

```
### If you have SELinux enabled on the system, run the following command.
```
chcon -R -t httpd_sys_content_t /svn/first-repo/
chcon -R -t httpd_sys_rw_content_t /svn/first-repo/
```


## 4. Configuring Apache Server

```bash
vim /etc/httpd/conf.d/svn.conf
```

svn.conf
```xml
<location /svn>
DAV svn
SVNParentPath /svn
</location>
```

```bash
systemctl reload httpd.service
```


<br/><br/>

Now, we should be able to access the SVN using your favorite browser, we'll need to go to http://our-ip-address/svn/first-repo . It wont ask us any password to view so, it makes our SVN repository publicly accessible. If we want to secure our SVN repository then, we'll need to follow the steps below.

<br/><br/>
  
## 5. Securing SVN: (Optional)

```bash
vim /etc/httpd/conf.d/svn.conf
```

svn.conf
```xml
<location /svn>
DAV svn
SVNParentPath /svn/
AuthType Basic
AuthName "Authorization Realm"
AuthUserFile /etc/subversion/svn.users
Require valid-user
</location>
```


Created the password file for the user.

```bash
htpasswd -cm /etc/subversion/svn.users user0
```

```bash
systemctl reload httpd.service
```


## 6. Testing Subversion

```bash
mkdir -p /tmp/repository/project/{trunk,branches,tags}

svn import -m 'Initial Import' /tmp/repository/project/ http://ip/svn/first-repo
```


<br/><br/>

## Ref
1. https://linoxide.com/linux-how-to/install-apache-svn-subversion-centos-7/
