# Ansible Role linuxfabrik.lfops.moodle

This role installs [Moodle](https://moodle.org/), an open source Learning Management System (LMS) designed to provide educators with tools for creating and managing online courses and learning activities.

This role supports the following Moodle versions:

* v4.1 LTS
* v4.5 LTS

Setting the version manually:

* `ansible-playbook --inventory=myansinv linuxfabrik.lfops.setup_moodle --tags=moodle --extra-vars="moodle__github_version=v4.1.12"`


## Mandatory Requirements

* Attention: Moodle has very specific version requirements regarding PHP and Redis. See https://moodledev.io/general/development/policies/php.
* Install a web server (for example Apache httpd), and configure a virtual host for Moodle. This can be done using the [linuxfabrik.lfops.apache_httpd](https://github.com/Linuxfabrik/lfops/tree/main/roles/apache_httpd) role.
* Install PHP v8.1. This can be done using the [linuxfabrik.lfops.repo_remi](https://github.com/Linuxfabrik/lfops/tree/main/roles/repo_remi) and [linuxfabrik.lfops.php](https://github.com/Linuxfabrik/lfops/tree/main/roles/php) role.
* Install Redis v7.2. This can be done using the [linuxfabrik.lfops.repo_remi](https://github.com/Linuxfabrik/lfops/tree/main/roles/repo_remi) and [linuxfabrik.lfops.redis](https://github.com/Linuxfabrik/lfops/tree/main/roles/redis) role.

If you use the ["Setup Moodle" Playbook](https://github.com/Linuxfabrik/lfops/blob/main/playbooks/setup_moodle.yml), this is automatically done for you.


## Tags

| Tag         | What it does                 |
| ---         | ------------                 |
| `moodle`      | Download tar.gz locally, put it on the host, run the Moodle installer, fix file permissions, set up Moodle's cron job, enable/disable it, and set up Moosh. |
| `moodle:cron` | Set up Moodle's cron job. |
| `moodle:moosh` | Set up Moosh. |
| `moodle:moosh_run` | Runs user-defined Moosh commands. Does not run automatically, manually call with `--tags moodle:moosh_run`. |
| `moodle:state` | Enable/disable Moodle's cron job. |


## Mandatory Role Variables

| Variable | Description |
| -------- | ----------- |
| `moodle__admin` | Dictionary. The admin account to create. Subkeys: <ul><li>`username`: Mandatory, string. Username for the Moodle admin account.</li><li>`password`: Mandatory, string. Password for the Moodle admin account.</li><li>`email`: Email address for the Moodle admin account. Visible for everyone.</li></ul> |
| `moodle__database_login` | Dictionary. The user account for accessing the Moodle SQL database. Currently, only MySQL/MariaDB is supported. Subkeys: <ul><li>`username`: Mandatory, string. Username for the Moodle database account.</li><li>`password`: Mandatory, string. Password for the Moodle database account.</li></ul> |
| `moodle__url` | String. The public facing Moodle URL, including `http://` or `https://` (the latter if you are behind a TLS-terminating Reverse Proxy). |
| `moodle__version` | The major and minor of the Moodle version to install. The patch version is automatically gathered, but can be overwritten using `--extra-vars="moodle__github_version=v4.1.12"`. Recommended to set this to a LTS version, see https://endoflife.date/moodle. |

Example:
```yaml
# mandatory
moodle__admin:
  username: 'moodle-admin'
  password: 'linuxfabrik'
  email: 'learning@example.com'
moodle__database_login:
  username: 'moodle'
  password: 'linuxfabrik'
moodle__url: 'https://learning.example.com'
moodle__version: '4.5'
```


## Optional Role Variables

| Variable | Description | Default Value |
| -------- | ----------- | ------------- |
| `moodle__behind_reverse_proxy` | Behind a Reverse Proxy (assuming it terminates TLS) or not? | `true` |
| `moodle__data_dir` | String. Location of the moodle data folder, must not be web accessible. | `'/data'` |
| `moodle__database_host` | String. Database host. | `'localhost'` |
| `moodle__database_login_host` | String. The Host-part of the SQL database user. | `localhost` |
| `moodle__database_name` | String. Database name. | `'moodle'` |
| `moodle__database_port` | Number. Database port. | `3306` |
| `moodle__database_socket` | String. Path to database socket file. | `'/var/lib/mysql/mysql.sock'` |
| `moodle__database_table_prefix` | String. Table prefix for all database tables. | `'mdl_'` |
| `moodle__install_dir` | String. Database socket path. | `'/var/www/html/moodle'` |
| `moodle__on_calendar_cron`| String. Run interval of Moodle `cron.php`. It is recommended that the Systemd timer is run every minute, as required for asynchronous activity deletion when using the recycle bin. | `'minutely'` |
| `moodle__site_fullname` | String. This name appears at the top of every page above the navigation bar. | `'Moodle powered by Linuxfabrik'` |
| `moodle__site_shortname` | String. The short name appears at the beginning of the navigation bar as a link back to your site front page. | `'Moodle'` |
| `moodle__site_summary` | String. This summary can be displayed on the left or right of the front page using the course/site summary block. The summary is also used as the HTML metadata description in some themes, for the front page of the site. This is not generally seen by users, but can be useful for search engines that index the page. | `''` |
| `moodle__sitepreset` | String. Admin site preset to be applied during the installation process. | `''` |
| `moodle__supportemail` | String. Email address for support and help. | `''` |
| `moodle__timer_cron_enabled` | Boolean. Enables/disables Systemd-Timer for running `/path/to/moodle/admin/cli/cron.php`. | `true` |
| `moodle__upgradekey` | String. The upgrade key to be set in the `config.php`, leave empty to not set it. | `''` |

Example:
```yaml
# optional
moodle__behind_reverse_proxy: true
moodle__data_dir: '/data'
moodle__database_host: 'localhost'
moodle__database_login_host: 'localhost'
moodle__database_name: 'moodle'
moodle__database_port: 3306
moodle__database_socket: '/var/lib/mysql/mysql.sock'
moodle__database_table_prefix: 'mdl_'
moodle__install_dir: '/var/www/html/moodle'
moodle__on_calendar_cron: 'minutely'
moodle__site_fullname: 'Moodle powered by Linuxfabrik'
moodle__site_shortname: 'Moodle'
moodle__site_summary: 'My Site Summary'
moodle__sitepreset: ''
moodle__supportemail: 'support@example.com'
moodle__timer_cron_enabled: true
moodle__upgradekey: ''
```


## Optional Role Variables - Moosh

This role supports the administration of Moodle using Moosh. This is done by defining a list of user-defined commands and executing them using the `moodle:moosh_run` tag. Commands can be defined just like on the command line, so you are free to do whatever you want with Moosh.

| Variable | Description | Default Value |
| -------- | ----------- | ------------- |
| `moodle__moosh_commands` | List of strings. List of commands that Moosh should execute. | `[]` |
| `moodle__moosh_download_url` | String. Moosh download URL on moodle.org. | `'https://moodle.org/plugins/download.php/34835/moosh_moodle45_2025020800.zip'` |

Example:
```yaml
# optional
moodle__moosh_commands:
  - 'user-create --firstname=Linus --lastname=Löffel --city=Zurich --country=Switzerland linus'
  - 'user-list --sort'
moodle__moosh_download_url: 'https://moodle.org/plugins/download.php/34835/moosh_moodle45_2025020800.zip'
```


## License

[The Unlicense](https://unlicense.org/)


## Author Information

[Linuxfabrik GmbH, Zurich](https://www.linuxfabrik.ch)
