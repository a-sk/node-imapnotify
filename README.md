# imapnotify

Execute scripts on IMAP mailbox changes (new/deleted/updated messages) using IDLE.

# config

```javascript
{
  "host": "",
  "port": ,
  "tls": true,
  "tlsOptions": { "rejectUnauthorized": false },
  "username": "",
  "password": "",
  "onNotify": "/usr/bin/mbsync test-%s",
  "onNotifyPost": {"mail": "/usr/bin/notmuch new && notify-send 'New mail arrived'"},
  "boxes":
    [
      "box1",
      "box2/box3"
    ]
}
```

```
	onNotify:
		[string]: shell command to run on any event
		[object]: shell commands to run on for each type of event
			keys: "mail" for new mail, "update" for existing messages updates, "expunge" for messages deletions
    onNotifyPost:
		[string]: shell command to run after onNotify event
		[object]: shell commands to run after onNotify for each type of event
			keys: "mail" for new mail, "update" for existing messages updates, "expunge" for messages deletions
```

### extra options

```
    onSIGNAL: a command to run when `SIGNAL` is received.
    onSIGNALpost: a command to run after onSINGAL
``` 

Example:

```javascript
{
  ...
  "onSIGTERM": "/usr/bin/mbsync -a",
  "onSIGTERMpost": "echo 'Bye-Bye'"
  ...
}
```

# config as a node module

Since we load the config file with require(), we can get away with any nodejs 
module instead of just json.
This allows us to be more flexible with the configuration.

In particular, you one can use it to load password from a script rather than
having to store it in plain text.

Important: config-module must be `requirable` (in your $NODE_PATH or
given by an absolute path)

Using the latest (>0.12) version of nodejs, one can write myconfig.js as
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
    exports.onNotify = "<sync command>"
    exports.onNotifyPost = "<command>"
    exports.boxes = [ "box1", "box2", "some/other/box" ];
```

Then you can use

```bash
    $ imapnotify -c ~/.config/imap_notify/myconfig.js
```
Thanks Matthew, for pointing that out!

## substitutions

`%s` in `onNotify` and `onNotifyPost` is replaced by the box name.

`/` symbol (slash) is replaced by `-` symbol (minus) so that
`inbox/junk` becomes `inbox-junk`


# example mbsync configuration

```
IMAPAccount      example
Host             imap.example.com
User             test@example.com
Pass             secret
UseIMAPS         yes

IMAPStore  example-remote
Account    example

MaildirStore  example-local
Path ~/Maildir/mail/example/
Inbox ~/Maildir/mail/example/Inbox
Flatten .


Channel example-inbox
Master :example-remote:INBOX
Slave  :example-local:INBOX

Channel example-github
Master :example-remote:github
Slave  :example-local:github

Channel example-lists-vim-announce
Master :example-remote:lists/vim-announce
Slave  :example-local:lists/vim-announce
```

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
