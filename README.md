[![CI](https://github.com/de-it-krachten/ansible-role-rainloop/workflows/CI/badge.svg?event=push)](https://github.com/de-it-krachten/ansible-role-rainloop/actions?query=workflow%3ACI)


# ansible-role-rainloop

Manages rainloop



## Dependencies

#### Roles
None

#### Collections
- community.general
- ansible.posix

## Platforms

Supported platforms

- Red Hat Enterprise Linux 7<sup>1</sup>
- Red Hat Enterprise Linux 8<sup>1</sup>
- Red Hat Enterprise Linux 9<sup>1</sup>
- CentOS 7
- RockyLinux 8
- RockyLinux 9
- OracleLinux 8
- OracleLinux 9
- AlmaLinux 8
- AlmaLinux 9
- Debian 10 (Buster)
- Debian 11 (Bullseye)
- Ubuntu 18.04 LTS
- Ubuntu 20.04 LTS
- Ubuntu 22.04 LTS

Note:
<sup>1</sup> : no automated testing is performed on these platforms

## Role Variables
### defaults/main.yml
<pre><code>
# product version
rainloop_version: latest

# release api
rainloop_api: https://api.github.com/repos/RainLoop/rainloop-webmail/releases/latest

# release url
rainloop_url: https://github.com/RainLoop/rainloop-webmail/releases/download/v{{ rainloop_version }}/rainloop-legacy-{{ rainloop_version }}.zip

# directory to put temporary download file into
rainloop_tmpdir: /tmp

# local file
rainloop_file: '{{ rainloop_tmpdir }}/rainloop-community-latest.zip'

# temporary directory
rainloop_tmp: "{{ rainloop_tmpdir }}/rainloop-tmp"

# OS user/group
rainloop_user: "{{ 'apache' if rainloop_web_server == 'apache' else 'www-data' }}"
rainloop_group: "{{ 'apache' if rainloop_web_server == 'apache' else 'www-data' }}"

# Restrict access to local loopback
rainloop_restrict_access: false

# dict of rainloop settings
rainloop_settings: {}

# Default domain template
rainloop_domain_template: domain.j2
</pre></code>


### vars/family-RedHat.yml
<pre><code>
# list of packages
rainloop_packages:
  - unzip
  - rsync

# default web service
rainloop_web_server: apache

# web service
rainloop_web_service: "{{ 'httpd' if rainloop_web_server == 'apache' else 'nginx' }}"

# php socket
rainloop_php_socket: /var/run/php/php-fpm.sock
</pre></code>

### vars/family-Debian.yml
<pre><code>
# list of packages
rainloop_packages:
  - unzip
  - rsync

# default web service
rainloop_web_server: nginx

# web service
rainloop_web_service: "{{ 'apache2' if rainloop_web_server == 'apache' else 'nginx' }}"

# php socket
rainloop_php_socket: /var/run/php/php-fpm.sock
</pre></code>



## Example Playbook
### molecule/default/converge.yml
<pre><code>
- name: sample playbook for role 'rainloop'
  hosts: all
  become: "yes"
  vars:
    openssl_fqdn: server.example.com
    apache_fqdn: "{{ openssl_fqdn }}"
    apache_ssl_key: "{{ openssl_server_key }}"
    apache_ssl_crt: "{{ openssl_server_crt }}"
    apache_ssl_chain: "{{ openssl_server_crt }}"
    nginx_server_name: webmail.example.com
    nginx_ssl_key: "{{ openssl_server_key }}"
    nginx_ssl_crt: "{{ openssl_server_crt }}"
    nginx_root: /var/www/webmail.example.com/public_html
    nginx_logdir: /var/www/webmail.example.com/log
    rainloop_vhost: webmail.example.com
    rainloop_domain: example.com
    rainloop_ssl_key: "{{ openssl_server_key }}"
    rainloop_ssl_crt: "{{ openssl_server_crt }}"
    rainloop_ssl_chain: "{{ openssl_server_crt }}"
    rainloop_path: /var/www/webmail.example.com/public_html
    rainloop_log: /var/www/webmail.example.com/log
    rainloop_web_server: "{{ 'apache' if ansible_os_family == 'RedHat' else 'nginx' }}"
  roles:
    - openssl
    - {'role': 'apache', 'when': "ansible_os_family == 'RedHat'"}
    - {'role': 'nginx', 'when': "ansible_os_family == 'Debian'"}
    - php
  tasks:
    - name: Include role 'rainloop'
      ansible.builtin.include_role:
        name: rainloop
</pre></code>
