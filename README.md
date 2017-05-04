Ansible Role for Backup gem
===========================

Ansible role that enables backups using [backup](https://github.com/backup/backup) gem.

Role Variables
--------------

```yaml
backup_user: 'deploy'
root_path: '/etc/Backup'
tmp_path: '/tmp'
data_path: '/var/backups'

models: []
#  - name: 'database_backup'
#    backup_database:
#      postgresql:
#        dbname: :all
#        host: "localhost"
#        port: 5432
#        username: "postgres"
#        password: "postgres"
#        socket: "/tmp/pg.sock"
#      mysql:
#        dbname: :all
#        host: "localhost"
#        port: 3306
#        username: "mysql"
#        password: "mysql"
#        socket: "/tmp/mysql.sock"

#    backup_encryption: False
#    encrypt_password: 'pa$$w0rd'
#    encrypt_password_file: ''
#    backup_compression: True

#    backup_storage:
#      local:
#        path: '/var/backups/'
#        keep: 14
#      dropbox:
#        api_key: ''
#        api_secret: ''
#        path: ''
#        keep: 14
#      s3:
#        access_key: ''
#        secret_access_key: ''
#        region: ''
#        bucket: ''
#        path: ''
#        keep: 14
#      scp:
#        username: ''
#        password: ''
#        ip: ''
#        port: ''
#        path: ''
#        keep: 14

#    backup_notifier:
#      mail:
#        from: ''
#        to: ''
#        cc: ''
#        bcc: ''
#        reply_to: ''
#        address: ''
#        port: ''
#        domain: ''
#        username: ''
#        password: ''
#        authentication: 'plain'
#        encryption: ':starttls'
#        on_success: 'true'
#        on_warning: 'true'
#        on_failure: 'true'
#      slack:
#        url: ''
#        channel: ''
#        username: ''
#        on_success: 'true'
#        on_warning: 'true'
#        on_failure: 'true'
#      http_post:
#        on_success: 'true'
#        on_warning: 'true'
#        on_failure: 'true'
#        uri: ''

#    cron:
#      minute: 15
#      hour: 1
#      day: '*'
#      weekday: '*'
```
