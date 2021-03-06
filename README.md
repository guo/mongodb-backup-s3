# mongodb-backup-s3 for v4.2

This image runs mongodump to backup data using cronjob to an s3 bucket

## Changes after forking

- support V4.2
- use MONGODB_URI as auth instead of PORT/USER/PASS auth.

## Usage:

```
docker run -d \
  --env AWS_ACCESS_KEY_ID=awsaccesskeyid \
  --env AWS_SECRET_ACCESS_KEY=awssecretaccesskey \
  --env BUCKET=s3bucket
  --env MONGODB_URI=URI \
  guos/mongodb-v4.2-backup-s3
```

Recommend to use caprover to config your auto-pilot mongodb backup jobs.

## use Caprover

1. add a new app
2. deploy from docker using `guos/mongodb-v4.2-backup-s3` image
3. add config var as follows
```
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
BUCKET=
BACKUP_FOLDER=
MONGODB_URI=mongodb+srv://
CRON_TIME=0 3 * * *
TZ=US/Eastern
A=1
CRON_TZ=US/Eastern
INIT_BACKUP=true
INIT_RESTORE=
DISABLE_CRON=
```
4. keep it running


--- old content ---

Add to a docker-compose.yml to enhance your robotic army:

For automated backups
```
mongodbbackup:
  image: 'halvves/mongodb-backup-s3:latest'
  links:
    - mongodb
  environment:
    - AWS_ACCESS_KEY_ID=myaccesskeyid
    - AWS_SECRET_ACCESS_KEY=mysecretaccesskey
    - BUCKET=my-s3-bucket
    - BACKUP_FOLDER=prod/db/
  restart: always
```

Or use `INIT_RESTORE` with `DISABLE_CRON` for seeding/restoring/starting a db (great for a fresh instance or a dev machine)
```
mongodbbackup:
  image: 'halvves/mongodb-backup-s3:latest'
  links:
    - mongodb
  environment:
    - AWS_ACCESS_KEY_ID=myaccesskeyid
    - AWS_SECRET_ACCESS_KEY=mysecretaccesskey
    - BUCKET=my-s3-bucket
    - BACKUP_FOLDER=prod/db/
    - INIT_RESTORE=true
    - DISABLE_CRON=true
```

## Parameters

`AWS_ACCESS_KEY_ID` - your aws access key id (for your s3 bucket)

`AWS_SECRET_ACCESS_KEY`: - your aws secret access key (for your s3 bucket)

`BUCKET`: - your s3 bucket

`BACKUP_FOLDER`: - name of folder or path to put backups (eg `myapp/db_backups/`). defaults to root of bucket.

`MONGODB_HOST` - the host/ip of your mongodb database

`MONGODB_PORT` - the port number of your mongodb database

`MONGODB_USER` - the username of your mongodb database. If MONGODB_USER is empty while MONGODB_PASS is not, the image will use admin as the default username

`MONGODB_PASS` - the password of your mongodb database

`MONGODB_DB` - the database name to dump. If not specified, it will dump all the databases

`EXTRA_OPTS` - any extra options to pass to mongodump command

`CRON_TIME` - the interval of cron job to run mongodump. `0 3 * * *` by default, which is every day at 03:00hrs.

`TZ` - timezone. default: `US/Eastern`

`CRON_TZ` - cron timezone. default: `US/Eastern`

`INIT_BACKUP` - if set, create a backup when the container launched

`INIT_RESTORE` - if set, restore from latest when container is launched

`DISABLE_CRON` - if set, it will skip setting up automated backups. good for when you want to use this container to seed a dev environment.

## Restore from a backup

To see the list of backups, you can run:
```
docker exec mongodb-backup-s3 /listbackups.sh
```

To restore database from a certain backup, simply run (pass in just the timestamp part of the filename):

```
docker exec mongodb-backup-s3 /restore.sh 20170406T155812
```

To restore latest just:
```
docker exec mongodb-backup-s3 /restore.sh
```

## Acknowledgements

  * forked from [futurist](https://github.com/futurist)'s fork of [tutumcloud/mongodb-backup](https://github.com/tutumcloud/mongodb-backup)
