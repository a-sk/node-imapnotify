# imapnotify

Execute scripts on new messages using IDLE IMAP command

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
# config as a node module

Since we load the config file with require(), we can get away with any nodejs 
module instead of just json.
This allows us to be more flexible with the configuration.

In particular, you one can use it to load password from a script rather than
having to store it in plain text.

Using the latest (git) version of nodejs, one can write myconfig.js as
follows.  Assuming the script ~/getpass.sh prints out your password.
(execSync is a v0.12 feature)
```javascript
    var child_process = require('child_process');

    function getStdout(cmd) {
        var stdout = child_process.execSync(cmd);
        return stdout.toString().trim();
    }

    exports.host = "<host>"
    exports.port = <port>;
    exports.tls = true;
    exports.tlsOptions = { "rejectUnauthorized": false };
    exports.username = "<user>";
    exports.password = getStdout("~/getpass.sh");
    exports.onNewMail = "<sync command>"
    exports.onNewMailPost = "<command>"
    exports.boxes = [ <boxes> ];
```

Then you can use

```bash
    $ imapnotify -c ~/.config/imap_notify/myconfig.js
```
Thanks Matthew, for pointing that out!

## substitutions

`%s` in `onNewMail` and `onNewMailPost` values is replaced by box name.

`/` symbol (slash) is replaced by `-` symbol (minus)

# install

npm:

```
npm install -g imapnotify
```

archlinux aur package:

```
yaourt -S nodejs-imapnotify
```
(bonus: systemd.service file)

# usage

```
imapnotify -c /path/to/config
```
