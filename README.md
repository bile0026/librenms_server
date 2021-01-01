# Librenms_server
deploy latest master branch of Librenms core server.

Work in Progress, not ready for prime time yet.

Curently only tested on CentOS 8/Ubuntu 20.04/Debian 10.7, and Nginx. Plans to work out Debian and Apache options eventually.

# Current issues:
1. Have to run a couple MySQL Queries after setup to fix database schema. (running validate after install will show you what to run). This appears to be an issue with librenms process, not this playbook. https://community.librenms.org/t/report-database-schema-issues-here/945/506
  * You can run the `mysql_fix.yml` playbook after the web install to fix these also. This has to be done after the database build step.
2. On CentOS, lnms command doesn't work properly. /usr/local/bin is not created in the service account $PATH

* NOTE:
- Make sure to use strong passwords, don't leave the defaults in this playbook.
- Currently disabled selinux until I have time to setup the proper configs.
- If you want to deploy a specific tag/release from git, uncomment the "version" parameter in the git clone task.

# How to use:
1. If running in ansible not AWX/Tower, first install required roles/collections.
    * `ansible-galaxy role install -r roles/requirements.txt`
    * `ansible-galaxy collection install -r collections/requirements.txt`
2. Modify variables as required. (see end of the readme for variables)
3. Run the playbook
    * i.e. `ansible-playbook -i hosts deploy_librenms_server.yml -u <user> -k/--key-file <priv_key_path> -K`
4. Once you get a successful playbook run, log into the web interface to finish the install. 
    * http://<core_server_fqdn>/install
5. You should now have a functional Librenms installation.

# Extras:
To deploy remote/additional pollers check out https://github.com/bile0026/librenms_poller

# Variables:
```
update_channel: release
# nginx or apache
webserver: nginx
time_zone: America/Chicago
php_version: 7.4 # for ubuntu installs
service_account: librenms
core_server_fqdn: "{{ ansible_facts.default_ipv4.address }}"

# snmp v2c string
snmp_string: public
# snmp v3 credentials
snmp_v3_auth:
  authlevel: AuthPriv
  authname: snmp_user
  authpass: pass
  authalgo: MD5
  cryptopass: pass
  cryptoalgo: AES
# snmp scanning options
auto_scan_subnets:  # subnets included in automatic discovery
  - 10.1.10.0/24
  - 10.1.40.0/24
exclude_scan_subnets:  # subnets excluded from automatic discovery
  - 10.1.10.1/32
  - 10.1.40.1/32

# mariadb vars
mariadb_databases:
  - name: librenms
    encoding: utf8
    collation: utf8_unicode_ci
mariadb_users:
  - name: librenms
    host: "%"
    password: librenms
    priv: 'librenms.*:ALL'
```
