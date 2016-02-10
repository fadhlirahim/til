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