# Week 5 Shared Website Permissions

## The problem

Give an intern access to a shared website while keeping the credentials file private to its owner.

## The commands and observed output

These tests were executed in Ubuntu WSL. Required administrative commands were run as root. The project owner is labstudent.

```text
$ sudo -u intern cat /srv/driftwood/site/index.html
cat: /srv/driftwood/site/index.html: Permission denied

$ usermod -aG otterstaff intern
$ id intern
uid=1001(intern) gid=1002(intern) groups=1002(intern),1001(otterstaff)

$ sudo -u intern cat /srv/driftwood/site/index.html
<h1>Driftwood Books</h1>

$ sudo -u intern cat /srv/driftwood/private/credentials.txt
cat: /srv/driftwood/private/credentials.txt: Permission denied
```

## How it works

Initially, intern cannot access the website because other users have no permissions on the project directories. After intern joins otterstaff, a newly launched command can use the group permissions to reach and read index.html. The credentials file remains protected by mode 600, which allows only its owner to read and write it.
