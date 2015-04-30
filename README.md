## Atlassian services

    Version: 0.1.0

This repository holds a dockerized orchestration of the Atlassian web apps
Jira, Stash and Confluence. To simplify the usermangement Crowd is also
included. For more information on the apps please refere to the offical
Atlassian websites:

- [Jira][1]
- [Stash][2]
- [Confluence][3]
- [Crowd][4]

### Prerequisites

In order to run this apps you need to make sure you're running at least
[docker 1.6.0][5] and [docker-compose 1.2.0][6]. For detailed installation
instructions please refere to the origin websites:

  - [https://docs.docker.com/installation][7]
  - [https://docs.docker.com/compose][8]

### Deploy/Update the application

    # rebuild the docker images
    $ docker-compose build

    # start the docker images
    $ docker-compose up -d

    # inspect the logs
    $ docker-compose logs

If you deploy the apps for the first time you may need to restore the database
from a backup for each app and adapt the database connection settings!

### Develop Mode / Debug an image

    # start in development mode
    $ docker-compose -f docker-compose-dev.yml up

    # execute a bash shell inside a running container
    $ docker exec -it atlassian_stash_1 bash

### First run

If you start this orchestration for the first time, a handy feature is to
import your old data. If you're e.g. moving everything to another server
you can put your database backups into the tmp folder and the db initscript
will pick them up automagically on the first run.

    # move your jira db backup file to tmp (filename is important).
    $ mv jira_backup.sql tmp/jira.dump

    # unpack your jira-home backup archive
    $ tar xzf jira-home.tar.gz --strip=1 -C jira-home

### Backup the home folders

    $ mkdir -p backup/$(date +%F)
    $ for i in crowd confluence stash jira; do \
      tar czf backup/$(date +%F)/$i-home.tgz $i-home; done

### Backup the PostgreSQL data

    # backup the confluence database
    $ docker run -it --rm --link atlassian_database_1:db -v $(pwd):/tmp \
        postgres sh -c 'pg_dump -U confluence -h "$DB_PORT_5432_TCP_ADDR" \
        -w confluence > /tmp/confluence.dump'

    # backup the stash database
    $ docker run -it --rm --link atlassian_database_1:db -v $(pwd):/tmp \
        postgres sh -c 'pg_dump -U stash -h "$DB_PORT_5432_TCP_ADDR" \
        -w stash > /tmp/stash.dump'

    # backup the jira database
    $ docker run -it --rm --link atlassian_database_1:db -v $(pwd):/tmp \
        postgres sh -c 'pg_dump -U jira -h "$DB_PORT_5432_TCP_ADDR" \
        -w jira > /tmp/jira.dump'

    # backup the crowd database
    $ docker run -it --rm --link atlassian_database_1:db -v $(pwd):/tmp \
        postgres sh -c 'pg_dump -U crowd -h "$DB_PORT_5432_TCP_ADDR" \
        -w crowd > /tmp/crowd.dump'

### Restore the PostgreSQL data

    # restore the confluence database backup
    $ docker run -it --rm --link atlassian_database_1:db -v $(pwd):/tmp \
        postgres sh -c 'pg_restore -U confluence -h "$DB_PORT_5432_TCP_ADDR" \
        -n public -w -d confluence /tmp/confluence.dump'

    # restore the stash database backup
    $ docker run -it --rm --link atlassian_database_1:db -v $(pwd):/tmp \
        postgres sh -c 'pg_restore -U stash -h "$DB_PORT_5432_TCP_ADDR" \
        -n public -w -d stash /tmp/stash.dump'

    # restore the jira database backup
    $ docker run -it --rm --link atlassian_database_1:db -v $(pwd):/tmp \
        postgres sh -c 'pg_restore -U jira -h "$DB_PORT_5432_TCP_ADDR" \
        -n public -w -d jira /tmp/jira.dump'

    # restore the crowd database backup
    $ docker run -it --rm --link atlassian_database_1:db -v $(pwd):/tmp \
        postgres sh -c 'pg_restore -U crowd -h "$DB_PORT_5432_TCP_ADDR" \
        -n public -w -d crowd /tmp/crowd.dump'

---
[1]: https://www.atlassian.com/software/jira
[2]: https://www.atlassian.com/software/stash
[3]: https://www.atlassian.com/software/confluence
[4]: https://www.atlassian.com/software/crowd
[5]: https://docker.com
[6]: https://docs.docker.com/compose
[7]: https://docs.docker.com/installation
[8]: https://docs.docker.com/compose/#installation-and-set-up

