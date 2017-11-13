# Ansible Role for Backup gem

An ansible role that enables backups using [backup](https://backup.github.io/backup/v4/) gem. It allows you to configure the backup system for MySQL and PostgreSQL databases and automate it with the **Cron** utility. Various storages, notifiers and compressors are available.

The role includes the following tasks:
1. Install necessary Ruby packages.
2. Install the backup gem.
3. Create configuration directories at the paths specified in the role variables.
4. Configuration of backup process.
5. Presetting and configuration of Cron jobs.

This role can be run under all versions of Ubuntu.

## Requirements

Ruby (2.0.x - 2.3.x).

## Role Variables

Available variables are listed below, along with default values (see defaults/main.yml):

```yaml
# Required variables:
backup_user: 'deploy'             # Run backup tasks under the user
backup_root_path: '/etc/Backup'   # A root folder for all relative paths
backup_tmp_path: '/tmp'           # The path where backups are processed until they're stored
backup_data_path: '/var/backups'  # Working directory for persistent backups data

# Models for specifying a way of processing backups for used databases, storages etc:
backup_models: []                 # An array of models parameters
backup_databases: []              # An array of databases parameters
backup_storages: []               # An array of storages parameters
backup_notifiers: []              # An array of notifiers parameters
backup_encryptors: {}             # An encryptor parameters (OpenSSL only)
backup_compressors: {}            # A compressor parameters
```

You are able to describe any number of backup models. Each of them requires the following information to be specified:

```yaml
## Models:
backup_models:
  - name: ''                      # The model name
    databases: []                 # An array of databases
    notifiers: []                 # An array of notifiers
    storages: []                  # An array of storages
    cron: {}                      # Cron job parameters
```

The cron job can include the following parameters:
- minute - minute when the job should run (0-59, \*, \*/2, etc);
- hour - hour when the job should run (0-23, \*', \*/2, etc);
- day - day of the month when the job should run (1-31, \*, \*/2, etc);
- weekday - day of the week when the job should run (0-6 for Sunday-Saturday, \*, etc).

### Databases

The role provides support for databases:
- MySQL
- PostgreSQL

```yaml
backup_databases:
# MySQL parameters:
  - name: ''              # The variable name
    type: 'mysql'         # A database type
    db: ''                # Specify a database name; ':all' to track all MySQL databases
    host: '127.0.0.1'     # A database host or localhost by default
    port: 3306            # A port of the MySQL server, 3306 by default
    username: ''          # Your MySQL database connection credentials
    password: ''
    socket: ''            # A path to a socket file

# Similar PostgreSQL parameters:
  - name: ''
    type: postgresql
    db: ''
    host: '127.0.0.1'
    port: 5432
    username: 'postgres'
    password: ''
    socket: ''
```

### Encryptors and Compressors

If necessary you can add an encryption to your backups. Only OpenSSL encryption is available at the moment. To use it you should specify a password or a password file.

```yaml
# Specify an encryption password
backup_encryptors:
  password: ''        # A password string
# OR a password file
backup_encryptors:
  password_file: ''   # A path to a password file
```

The role provides two kinds of compressors (Gzip and Bzip2) which supports 9 levels of compression (from 1 to 9). Gzip is the fastest compressor and requires the least amount of memory. However, Bzip2 provides higher compression than Gzip. Only one compressor may be used for each model.

```yaml
backup_compressors:
  type: gzip
  level: 6            # Default level of compression for gzip
backup_compressors:
  type: bzip2
  level: 9            # Level of compression for bzip2, maximum value by default
```

### Storages

You are able to keep your backups in different storages:
- Local storage.
- Dropbox. To use the dropbox service as a backup storage, you need a dropbox account and a dropbox app.
- Amazon AWS (S3). You need an Amazon AWS (S3) account to use the storage.
- SCP.

```yaml
backup_storages:
# Parameters of local storage
  - name: ''                # The storage variable name
    type: local             # A type of the storage
    path: ''                # A path to backups
    keep: 14                # A number of copies which must be kept
# OR use 'keep' like a time parameter:
#   keep: Time.now - 2592000 # Remove all backups older than 1 month.

# Parameters of dropbox storage
  - name: ''
    type: dropbox
    api_key: ''             # Your dropbox account key
    api_secret: ''          # Your dropbox account secret key
    path: ''                # A path to backups
    keep: 14                # A number of copies which must be kept

# Parameters of Amazon storage
  - name: ''
    type: s3
    access_key: ''          # Your Amazon account access key
    secret_access_key: ''   # Your Amazon account secret access key
    region: ''              # Your AWS region
    bucket: ''              # Your AWS bucket
    path: ''                # A path to backups
    keep: 14                # A number of copies which must be kept

# Parameters of SCP
  - name: ''
    type: scp
    username: ''            # Your username and password
    password: ''
    ip: ''                  # A server IP
    port: 22                # A port to connect
    path: ''                # A path to backups
    keep: 14                # A number of copies which must be kept
```

### Notifiers

Different notifiers can be used to get notifications of successful and/or failed operations. The following notifiers are available now:
- Email
- Slack
- HTTP POST
- Command

```yaml
backup_notifiers:
# Send notification by email
  - name: email
    type: mail
    from: ''                  # Sender email address
    to: ''                    # Receiver email address
    cc: ''                    # CC address, optional
    bcc: ''                   # BCC address, optional
    reply_to: ''              # Reply_to email field, optional
    address: ''               # Specify a mail server and a port it uses
    port: '587'
    domain: ''                # A domain name
    username: ''              # The sender email credentials
    password: ''
    authentication: 'plain'
    encryption: ':starttls'
    on_success: 'true'        # Send notifications on successful backups operation
    on_warning: 'true'        # Send notifications when a warning was gotten
    on_failure: 'true'        # Send notifications in case of failed operation
```

Use next group of parameters to configure notifications with Slack.

```yaml
  - name: ''
    type: slack
    url: ''                   # The integration token
    channel: ''               # The channel to which messages will be sent, optional
    username: ''              # The username to display along with the notification, optional
    on_success: 'true'        # Send notifications on successful backups operation
    on_warning: 'true'        # Send notifications when a warning was gotten
    on_failure: 'true'        # Send notifications in case of failed operation
```

You are able to get notification through HTTP POST specifying the following parameters.

```yaml
  - name: ''
    type: http_post
    uri: ''                   # URI to post the notification to
    on_success: 'true'        # Send notifications on successful backups operation
    on_warning: 'true'        # Send notifications when a warning was gotten
    on_failure: 'true'        # Send notifications in case of failed operation
```

To use the Command notifier set necessary values to the next parameters.

```yaml
  - name: ''
    type: command
    cmd: ''                   # A command that needs to be executed
    args: '[]'                # And its arguments if necessary, an array of strings
    on_success: 'true'        # Send notifications on successful backups operation
    on_warning: 'true'        # Send notifications when a warning was gotten
    on_failure: 'true'        # Send notifications in case of failed operation
```

## Dependencies

- role: [user]() - an ansible role for managing users and group accounts. The `users_accounts` array should contain a user with name `backup_user`.
- role: [ruby]() - an ansible role that installs ruby with rbenv. The `backup_user` should be equal to one of `ruby_user`s and `ruby_group`s.

## Example playbook

```yaml
- hosts: all
  roles:
    - backup
  vars_files:
    - ./vars.yml
```

### Variables example

An example of the role variables is given below. It includes one model description which suggests tracking of all MySQL and PostgreSQL databases and using a local storage for keeping backups. Cron will execute the backup job every day at 4:15 AM. The role uses `gzip` to compress  data. OpenSSL encryption password is set as `pa$$w0rd`. Backups will be created at the path `/var/backups/` where last 7 copies will be stored. The example uses an email notification. This will make `hostname@example.com` send an email to `admin@example.com` every time a backup process returns a warning or is failed.

```yaml
backup_models:
  - name: 'daily'
    databases:
      - db-postgresql
      - db-mysql
    storages:
      - local                       # Keep all backups on the local storage
    notifiers:
      - email                       # Notify about backups events by email
    cron: { minute: 15, hour: 4 }   # A backup job is executed every day at 4:15 AM

backup_compressors:
  type: gzip                        # Use Gzip compressor

backup_encryptors:
  password: 'pa$$w0rd'

# Create backups for all MySQL and PostgreSQL databases
backup_databases:
  - name: 'db-postgresql'
    type: postgresql
    db: :all
  - name: 'db2-mysql'
    type: mysql
    db: :all

backup_storages:
  - name: local
    type: local
    path: '/var/backups/'           # A path to keep backups
    keep: 7                         # Keep last 7 backups

# Send an email on backup events (excepting successful ones)
backup_notifiers:
   - name: email
     type: mail
     from: 'hostname@example.com'
     to: 'admin@example.com'
     address: 'example.com'
     port: '587'
     domain: 'example.com'
     username: 'admin'
     password: 'password'
     on_success: 'false'
```

## License

Licensed under the [MIT License](https://opensource.org/licenses/MIT).
