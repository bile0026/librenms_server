# Librenms_server
deploy latest master branch of Librenms core server.

Work in Progress, not ready for prime time yet.

Curently only tested on CentOS 8 and Nginx. Plans to work out Debian/Ubuntu and Apache options eventually.

* NOTE:
Make sure to use strong passwords, don't leave the defaults in this playbook.
If you want to deploy a specific tag/release from git, uncomment the "version" parameter in the git clone task.

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
php_version: 7.4 # for debian installs
rrdcached_version: 1.7.0 # needs to match core server
service_account: librenms
core_server_fqdn: "{{ ansible_facts.default_ipv4.address }}"

# mysql vars
mysql_root_password_update: false
mysql_enabled_on_startup: true
mysql_root_password: super-secure-password
mysql_databases:
  - name: librenms
    encoding: utf8
    collation: utf8_unicode_ci
mysql_users:
  - name: librenms
    host: "%"
    password: librenms
    priv: "librenms.*:ALL"
```