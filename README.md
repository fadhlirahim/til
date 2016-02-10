# Git

## Changing all the commits with new author & email

Warning! Only do this if you're the sole author. It rewrites everything!

git filter-branch -f --env-filter "GIT_AUTHOR_NAME='Fadhli Rahim'; GIT_AUTHOR_EMAIL='fadhli@charaku.co'; GIT_COMMITTER_NAME='Fadhli Rahim'; GIT_COMMITTER_EMAIL='fadhli@charaku.co';" HEAD


# Sys Admin

## Logrotate

```
/opt/raynor/current/log/*.log {
  daily
  missingok
  rotate 7
  compress
  notifempty
  nocreate
  copytruncate
}
```

## Secure copy scp

scp ssh_username@remote_host:<file_in_remote_address> <your_local_file>

scp your_username@remotehost.edu:foobar.txt /some/local/directory