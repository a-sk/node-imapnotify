# imapnotify

Execute scripts on new messages using IDLE imap command

# config

```javascript
{
  "host": "",
  "port": ,
  "tls": true,
  "tlsOptions": { "rejectUnauthorized": false },
  "username": "",
  "password": "",
  "onNewMail": "/usr/bin/mbsync test-%s",
  "onNewMailPost": "/usr/bin/notmuch new",
  "boxes":
    [
      "box1",
      "box2/box3"
    ]
}
```

# install

npm:
    `npm install -g imapnotify`

archlinux aur package:
    `yaourt -S nodejs-imapnotify` (bonus: systemd.service file)

# usage

`imapnotify -c /path/to/config`
