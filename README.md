Description
===========

#### Development
A single Linux server running the latest version of
[Apache](https://www.apache.org//),
[PHP](http://www.php.net/).
and [MySQL 5.6](http://www.mysql.com/about/).

**This deployment is meant for development and testing scenarios only and is not
intended for production use**.


Instructions
===========

#### Getting Started
If you're new to PHP, the [Getting
Started](http://www.php.net/manual/en/getting-started.php) page can walk you
through the basics of PHP and it's uses.

If you chose to enable PHPMyAdmin, you can access it using the username and
password found in the Credentials section, with the URL listed under
PHPMyAdmin URL.

#### Scaling out
This deployment is configured to be able to scale out easily.  However,
if you are expecting higher levels of traffic, please look into one of our
larger-scale stacks.

#### Details of Your Setup
This deployment was stood up using [Ansible](http://www.ansible.com/).
Once the stack has been deployed, Ansible will not run again unless you update the
stack. **Any changes made to the configuration may be overwritten when the stack
is updated.**

[Apache](http://www.apache.org/) was configured to serve contents out
of /var/www/vhosts/example.com/, where example.com is the domain that
you provided during the setup.

[PHP](http://www.php.net/) was installed using the packaged version for
the chosen operating system, in order to ensure that you have the latest
stable release.

[Lsyncd](https://github.com/axkibe/lsyncd) has been installed in order to
sync static content from the Master server to all secondary servers.
When uploading content, it only needs to be uploaded to the Master node,
and will be automatically synchronized to all secondary nodes.  By default,
/var/www will be synchronized, unless otherwise specified during setup.

MySQL is being hosted on a Cloud Database instance, running MySQL 5.6.
Backups for MySQL are provided by [Holland](http://wiki.hollandbackup.org/),
which is running on the Master server.

Backups are configured using Cloud Backups.  The Master server is configured
to back up /var/spool/holland and /var/www once per week, and to retain
these backups for 30 days.

In order to restore the Database from backup, you will need to first restore
/var/spool/holland from the appropriate Cloud Backup.  After you have done so,
you will need to log into the Master server and restore the Holland backup
to the Cloud Database via the MySQL client.  For more assistance, please
contact your Support team.

Monitoring is configured to verify that Apache is running on both the Master
and all secondary servers, as well as that the Cloud Load Balancer is
functioning.  Additionally, the default CPU, RAM, and Filesystem checks
are in place on all servers.

#### Logging in via SSH
The private key provided with this deployment can be used to log in as
root via SSH. We have an article on how to use these keys with [Mac OS X and
Linux](http://www.rackspace.com/knowledge_center/article/logging-in-with-a-ssh-private-key-on-linuxmac)
as well as [Windows using
PuTTY](http://www.rackspace.com/knowledge_center/article/logging-in-with-a-ssh-private-key-on-windows).
This key can be used to log into all servers on this deployment.
Additionally, passwordless authentication is configured from the Master
server to all secondary servers.

#### Additional Notes
**This deployment is meant for development and testing scenarios only and is not
intended for production use**.


Requirements
============
* A Heat provider that supports the following:
  * OS::Heat::RandomString
  * OS::Heat::SoftwareConfig
  * OS::Heat::SoftwareDeployment
  * OS::Nova::KeyPair
  * OS::Nova::Server
  * Rackspace::Cloud::BackupConfig
  * Rackspace::CloudMonitoring::Check
  * Rackspace::RackConnect::PublicIP
* An OpenStack username, password, and tenant id.
* [python-heatclient](https://github.com/openstack/python-heatclient)
`>= v0.2.8`:

```bash
pip install python-heatclient
```

We recommend installing the client within a [Python virtual
environment](http://www.virtualenv.org/).

Parameters
==========
Parameters can be replaced with your own values when standing up a stack. Use
the `-P` flag to specify a custom parameter.

* `lamp_url`: Domain used to configure Apache (Default: example.com)
* `db_user`: Username to configure for SQL (Default: example)
* `db_name`: Database to configure in SQL (Default: example)
* `php_myadmin`: Enable or disable PHPMyAdmin (Default: False)
* `backup_email`: E-mail address to be notified for failing backups (Default: admin@example.com)
* `rc_network_name`: Name or UUID of RackConnected network to attach this server to 
* `lsync_directory`: This directory will be synchronized between all servers within this stack.  Leave blank to manually configure Lsyncd (Default: /var/www)
* `server_flavor`: Flavor of Cloud Server to be used for all servers in this stack (Default: 4 GB General Purpose v1)

Outputs
=======
Once a stack comes online, use `heat output-list` to see all available outputs.
Use `heat output-show <OUTPUT NAME>` to get the value of a specific output.

* `lamp_public_ip`: Server Public IP 
* `lamp_public_url`: Public URL 
* `lampmyadmin_url`: PHPMyAdmin URL (if enabled) 
* `mysql_user`: Database User 
* `mysql_database`: Database Name 
* `mysql_password`: Database Password 
* `ssh_private_key`: SSH Private Key 

For multi-line values, the response will come in an escaped form. To get rid of
the escapes, use `echo -e '<STRING>' > file.txt`. For vim users, a substitution
can be done within a file using `%s/\\n/\r/g`.
