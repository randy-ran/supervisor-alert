# supervisor-alert

[![License](https://img.shields.io/pypi/l/supervisor-alert.svg)](https://github.com/rahiel/supervisor-alert/blob/master/LICENSE.txt)

Are you using [Supervisor](http://supervisord.org) to manage processes on a
server? With supervisor-alert you can receive messages when the state of your
processes change. Be the first to know when your services die!

To send messages on Telegram you need to install and set up [telegram-send][]
first. You can also use any shell command to send the notifications.

[telegram-send]: https://github.com/rahiel/telegram-send

# Installation

Install supervisor-alert on your system:
``` shell
sudo pip install supervisor-alert
```

Then run:
``` shell
sudo supervisor-alert --configure
```
for the default configuration. This will send notifications over Telegram. Read
the next section to customize or if you dislike automatic configurations.

# Manual Configuration

Create the file `/etc/supervisor/conf.d/supervisor_alert.conf` as root:
``` shell
[eventlistener:supervisor_alert]
command=supervisor-alert --telegram
events=PROCESS_STATE_RUNNING,PROCESS_STATE_EXITED,PROCESS_STATE_FATAL
autostart=true
autorestart=true
user=supervisor_alert
```

This will send the notifications over Telegram, to use something else, for
example [ntfy][], pass in the command:
``` shell
command=supervisor-alert -c 'ntfy send'
```

This configuration will run the event listener as the user `supervisor_alert`.
It is a good practice to isolate services by running them as separate users (and
avoiding running them as root). Add the user with:
``` shell
sudo adduser supervisor_alert --system --no-create-home
```

Optionally, you can also subscribe to different supervisor events,
[look at the docs][events] to see on which ones you'd like to be notified.

Finally, load the config and start the event listener:

``` shell
sudo supervisorctl reread
sudo supervisorctl update
```

You should now receive your first alert, notifying you that `supervisor_alert`
has started running.

[ntfy]: https://github.com/dschep/ntfy
[events]: http://supervisord.org/events.html#event-types
