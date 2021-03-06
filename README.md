# Introduction
Hangupsbot is a bot designed for working with Google Hangouts.
* **Mentions:** If somebody mentions you in a room, receive a private hangout from the bot with details on the mention, including context, room and person who mentioned you.
* **Syncouts:** A syncout is two Hangout group chats that have their messages forwarded to each other, allowing seamless interaction between the two rooms. Primarily used to beat the 150 member chat limit, but it can also be used for temporarily connecting teams together to interact.
* **Hubot Integration:** Hangupsbot will allow you to connect to [Hubot](https://hubot.github.com/), instantly providing you access to hundreds of developed chat tools and plugins.
* **Lookups:** Google Sheets can be attached to the bot, which allows you to look up data in the spreadsheet instantly with just a few keywords.
* **Pushbullet API:** [Pushbullet](https://www.pushbullet.com/) is integrated.
* **Hooks and Sinks:** The bot has instructions for developing and attaching your own hooks and sinks, allowing the bot to interact with external services such as your company website, Google API's and much more.
* *And many additional features!* Eastereggs, games, nickname functionalities, the list goes on!

# IMPORTANT

This is a [fork](https://gitlab.sabah.io/eol/mogunsamang) of a [fork](https://github.com/xmikos/hangupsbot).

* To execute: `python3 hangupsbot.py`
* Any current tests will be in `<path of hangupsbot.py>/tests/`
* Please see the original documentation which is reproduced below
  (after the TODO section)

# Installation

Install python 3.4 from source

```
wget https://www.python.org/ftp/python/3.4.2/Python-3.4.2.tgz
tar xvf Python-3.4.2.tgz
cd Python-3.4.2
./config
make
make install
```

Install dependencies

```
pip3 install -r requirements.txt
```

# Users: Quickstart

1. You need to open a 1-on-1 conversation with the bot first and say "hello"
   (or anything!)
2. Then give yourself a @mention in another HO where the bot is participating
   as a group member

This procedure is necessary to let the bot know you're alive ;P
No seriously, the above steps are **required** to authorise two-way
communication between the bot and your own account.

## Usage of @mentions

Some general rules:
* If `mentionquidproquo` is ON, only users who have already said something to
  the bot in private will be able to @mention others.
* If a @mention matches multiple users, the bot will privately warn the user
  that too many users were selected. A list of all matching users will be
  provided as a guide to the user.
* A @mention must be at least 3 characters or longer (not counting the `@`)

A @mention matches the following:
* A part of the combined first name and last name of the user
  e.g. `@abc` matches `AB Chin` (bot sees `abchin`)
* A part of the combined first name and last name with spaces replaced by
  the underscore
  e.g. `@ab_c` matches `AB Chin` (bot sees `AB_Chin`)
* An exact match with a user nickname, if the user has previously has
  previously `/bot set nickname abc`
  e.g. `@abc` matches nickname `abc` but NOT nicknames `abcd` or `zabc`

# Admins: Quick Installation & Configuration

* `config.json` is found in two places:
  * one folder below `hangupsbot.py`;and later,
  * ...in `/root/.local/share/hangupsbot/` after first successful run
* add (or replace) your *user id* into the `admins` array in config.json

```
{
  "admins": [
    "104821221116551390464"
  ],
  "autoreplies": [
  ...
```
To find out how to get your user id, read on!

## Getting Your User ID

### first time admins with no existing bot

1. start the bot with a valid gmail account (not your actual account!)
2. open a hangout with the bot (using your actual account), say anything to it
3. login into the bot's gmail account and ensure chat is activated
4. open up invites, and accept your incoming invite (from your actual account)
5. restart the bot and it will dump out a conversation and users list
6. find your actual account user name, the chat_id will be listed next to it

### with an existing bot

join a group with an existing bot and issue this command `/bot whoami`, your
chat_id will be displayed

# Admins: Configuration

Configuration directives can be specified in `config.json`.

Most configuration directives can be specified **globally** or **per-conversation**.
* Global directives are always specified in the "root" of `config.json`.
* To specify a per-conversation directive, the same configuration option should
  be defined as `config.conversations[<conversation-id>].<configuration option>`.
* Per-conversation directives override global settings, if both are set.

## Mentions

`mentionquidproquo`
* default: `true`
* only users who have already initiated a 1-on-1 dialog with the bot will be
  able to use @mentions and alert other users

`mentionerrors`
* default: `false`
* outputs any problems with a @mention to the current conversation
* verbose output and should only be used for debugging
* alternative is to use `/bot mention <name-fragment> test`
  (see bot command below)

`mentionall`
* default: `true`
* enables/disables @all for mentions
* when set to `false`, admins and chat ids listed in `mentionallwhitelist`
  can still use **@all**
* users who are blocked from using @mentions (`mentionall == false`;
  not an admin; not whitelisted) will be notified privately if the bot
  already has a 1-on-1 with them

`mentionallwhitelist`
* default: `[]`
* allow listed chat_ids to use @all in mentions regardless of
  global/per-conversation `mentionall` setting

# Syncing Chats (Syncout)

Chats can be synced together, called a 'Syncout'. If a person says something in chat A, that message will be relayed into chat B by the bot, and vice versa, allowing multiple rooms to have conversations with each other. The primary use for this is to have more than 150 (the hangout limit) users talking to each other in the same room.

`syncing_enabled`
* default: false
* If enabled, will look for synced rooms and start relaying chats across the rooms
* Can only be enabled/disabled globally

`sync_rooms`
* a list of group ChatID's that the bot will attempt to keep synced
* it is possible to have more than one syncout instance per bot by having in your config.json:
```
"CONV1_ID": {  
  "sync_rooms": ["CONV1_ID", "CONV2_ID", ...]  
},  
"CONV2_ID": {  
  "sync_rooms": ["CONV1_ID", "CONV2_ID", ...]
},  
"CONV3_ID": {  
  "sync_rooms": ["CONV3_ID", "CONV4_ID", ...]  
}  
```
* NOTE: You will have to put the convID into the sync_room of that convID too (to allow for backward compatibility with a deprecated method). I know that this is highly redundant for now, but stick to this method and you won't have any problems in the future when the code is improved (I hope).

# User Triggers (`/me` prefix)

Special `/me` triggers are available to any user.
All these must be prefixed by `/me`, as in `/me <trigger>`.

`roll[s] [a] dice`
* Bot will (virtually) roll a dice and return the number **1-6**

`flip[s] [a] coin`
* Bot will (virtually) flip a coin and return **heads** or **tails**.

`draw[s] [a|an] <thing>`
* Bot will get a random <thing> from a box.
* The box must be "prepared" first (see Bot Commands below).
* Used for user lotteries
* `draw`-ing without a `<thing>` will always fetch an item from the
  **default** lottery/box (if its prepared). To draw from another box,
  `<thing>` must be supplied.
* Bot keeps tracks of people who have already participated in the draw.
  * Trying to draw again will make the bot remind you of your previous results.
  * WARNING: Records are not persisted between bot restarts!

# Bot Commands

All bot commands must be prefixed by `/bot`, as in `/bot <command>`.

## Administrative Commands

These are commands that can only be executed by admins, based on the default
configuration in `config.commands_admin`.

`users`
* Bot lists all users in current conversation.
* List will contain user G+ profile link and email (if available).

`user <string name>`
* Bot searches for users whose names contain `<string name>` in internal user list.
* Spaces are not allowed.
* The bot will search all users in all participating conversations.

`hangouts`
* Bot lists all participating conversations with additional details.
* Legend: `c` = commands enabled; `f` = forwarding enabled; `a` = auto-replies.

`rename <string title>`
* Bot renames the current conversation.
* Spaces in the title are allowed.
* Works with both 1-on-1 or group conversations.
* Note: 1-on-1 renames may not reflect their title properly on desktop clients.

`leave [<conversation id>]`
* Bot leaves the current hangout if `<conversation id>` not specified.

`easteregg <ponies|pitchforks|bikeshed|shydino> <number> <period>`
* Bot activates Hangouts easter-egg animation (varies by client).
* `<number>` is the amount of repetition with delay of `<period>` in seconds.

`quit`
* Kills the running bot process on the server with a disconnection.

`config get <key> [<subkey> [...]]`
* Bot reads config.json and displays contents of `config.<key>[.<subkey>...]`

`config set <key> [<subkey> [...]] "<value>"`
* Bot sets contents of `config.<key>[.<subkey>...]` to `<value>`
* `<value>` must be enclosed in double-quotes and is interpreted as JSON.
* Changes are saved instantly into `config.json`.
* WARNING: This command is low-level and can scramble the configuration.

`config append <key> [<subkey> [...]] "<value>"`
* Bot appends <value> to list at `config.<key>[.<subkey>...]`
* `<value>` must be enclosed in double-quotes and is interpreted as JSON.
* Only works if the key pointed at is an actual list.
* Usually used to add administrator ids to `config.admins`
* WARNING: This command is low-level and can scramble the configuration.

`config remove <key> [<subkey> [...]] "<value>"`
* Bot removes specified <value> from list at `config.<key>[.<subkey>...]`
* `<value>` must be enclosed in double-quotes and is interpreted as JSON.
* Only works if the key pointed at is an actual list.
* Usually used to remove administrator ids from `config.admins`
* WARNING: This command is low-level and can scramble the configuration.

## Standard Commands

These are commands that can be executed by any user, based on the default
configuration in `config.commands_admin`.

`help`
* Bot lists all supported commands.

`ping`
* Bot replies with a `pong`.

`echo <string anything>`
* Bot replies with `<string anything>` as the message.
* Spaces are allowed.

`pushbulletapi <apikey|false|0|-1>`
* Sets/unsets the pushbullet api key for current user.
* When user is @mentioned, bot will alert user through PushBullet.
* If the push fails, bot will revert to normal hangouts-based alert.
* `false`, `0` or `-1` Disables pushbullet integration for current user.

`dnd`
* Toggles global DND (Do Not Disturb) for current user.
* Bot will message user whether DND is toggled on or off.
* User will not receive alerts for @mentions.

`setnickname <nickname>`
* Bot sets the nickname for the current user
* `/whoami` will return the current nickname
* Call it again to re-set the `<nickname>` as preferred

`whoami`
* Bot replies with the current user information:
  * first name, nickname and `chat_id`, OR
  * full name and `chat_id`

`whereami`
* Bot replies with the conversation name and `conversation id`.

`mention <name fragment> [test]`
* Alias for @<name fragment>.
* Triggers the same mechanism for @mentions.
* `<name fragment>` cannot contain spaces.
* Adding optional second parameter `test` will show additional log information
  inside the current conversation when attempting to alert users. This
  can be used to check for any @mention errors with specific users.

`lookup <keyword>`
* Used in conjunction with a published Google Spreadsheet
* Need to enable in config: `spreadsheet_enabled`, `spreadsheet_url` and
  `spreadsheet_table_class`
* Will look up each row of a spreadsheet and if the keyword is found will
  return the whole row

`prepare [<things>] <listdef>`
* Bot prepares a list of `<things>` and puts them in a virtual box for lottery
  drawings.
  * If `<things>` is not supplied, then "default" will be used.
  * WARNING: Records are not persisted between bot restarts!
* `<listdef>` can be:
  * comma-separated list of values (no spaces!) e.g. `abc,ghi,xyz`
  * range of numbers e.g. 50-100
  * "numberTokens" e.g. 9long1short
* Each user must issue `/me draws` (or similar) to get a random item from the
  box. Note: `/me draw` always draws from "default" if available; to draw from
  another box, `/me draw [a|an] <thing>`

# Developers: Debugging

* Run the bot with the `-d` parameter e.g. `python3 hangupsbot.py -d` - this
  lowers the log level to `INFO` for a more verbose and informative log file.
* `tail` the log file, which is probably located at
  `/<user>/.local/share/hangupsbot/hangupsbot.log` - the location varies by
  distro!
* Console output (STDOUT) is fairly limited whatever the log level, so rely
  on the output of the log file instead.

# Developers: Extending the Bot

## Adding Hooks

Hooks allow extension of bot functionality by adding modular packages which
contain class methods. These methods are called on common chat events:
* `init`, called when the hook is first loaded (one-time per run)
* `on_chat_message`
* `on_membership_change`
* `on_rename`

A fully-functional chat logger is provided as part of the repo, and can be
found in `hangoutsbot/hooks/chatlogger/writer.php`. The chat logger logs each
chat the bot is operating in inside separate text files.

Note that hooks can use `config.json` as a configuration source as well. In
the case of the example chat logger, the following configuration is necessary:
```
...,
"hooks": [
{
  "module": "hooks.chatlogger.writer.logger",
  "config":
  {
    "storage_path": "<location to store chat log files>"
  }
}
],
...
```

## Adding your own (Web-Hook) Sinks

Sinks allow the bot to receive external events in the form of JSON-based web
requests. Presently the bot comes pre-packaged with several sinks:
* GitLab-compatible web hook sink that post git pushes in a hangout
* GitHub-compatible web hook sink that post git pushes in a hangout
* demo implementation that works with `hangupsbot/tests/send.py`

The sink/receiver is based on `BaseHTTPRequestHandler` -
`hangoutsbot/sinks/generic/simpledemo.py` is a very basic example.
Some recommendations:
* Always use SSL/TLS, a self-signed certificate is better than nothing
* Setting `config.jsonrpc[].name` to "127.0.0.1" will start the sink but only
  allow connections from localhost - use this to debug potentially unsafe sinks.

### GitLab Users: Web Hook Sink/Receiver

As noted previously, a GitLab-compatible sink is available for posting pushes into
a hangout - these are the configuration instructions:

#### configuring and starting the sink

Important: Still under development, subject to change

1. Generate a .pem file for SSL/TLS. It can be generated anywhere
   accessible to the script. **This is a mandatory step** as the sink will refuse
   to start without SSL/TLS. A self-signed certificate will do:
   ```
   openssl req -new -x509 -keyout server.pem -out server.pem -days 365 -nodes
   ```

2. Open the bot's `config.json` file and modify the `jsonrpc` key as follows:
   ```
   ...,
   "jsonrpc": [
     {
       "module": "sinks.gitlab.simplepush.webhookReceiver",
       "certfile": "<location of .pem file>",
       "port": 8000
     }
   ],
   ...
   ```

3. (Re-)start the bot

#### configuring gitlab

1. Determine which group hangout you want to receive GitLab events. In that
   hangout, execute `/bot whereami` - the bot will message the id for that
   specific hangout. Record the conversation id.
2. In your GitLab instance, access Project Settings > Web Hooks
3. Select which project events you want to be notified of and specify this URL:
   ```
   https://<your bot ip/domain name>:8000/<conversation id>/
   ```

4. After entering the above, **Add Web Hook**, then test the hook.

# Developers: TODO

* easier setup/configuration
* run as service ([cron](http://www.raspberrypi-spy.co.uk/2013/07/running-a-python-script-at-boot-using-cron/) works too!)
* better debug output

# Hubot Integration

## Requirements

* A functional (and running!) version of [hubot](https://github.com/github/hubot).
  Configuring this is beyond the scope of this document. Please refer to the
  installation instructions on the GitHub repo.
* A functional (also running!) copy of hangupsbot.

## Recommendations

* Both hubot and hangupsbot should run on the same server to reduce network
  connectivity issues

## Setup

1. `git clone` ALL THE THINGS (as usual).
2. `git submodule update --init hubot-web` to retrieve the forked copy of
   hubot-web that is compatible with hangupsbot.
3. Proceed to your hubot folder, and execute `npm install hubot-web` - this
   acquires the dependencies for [hubot-web](https://www.npmjs.com/package/hubot-web)
   and installs the npm version (which we will modify shortly).
4. Important: Overwrite the installed version of
   `hubot/node_modules/hubot-web/index.coffee` with `hubot-web/index.coffee`
   from the forked version inside the submodule.
5. Modify your working hangupsbot config.json and add the following
   sinks/hooks:
```
"hooks": [
    {
        "config": {
            "HUBOT_URL": "http://127.0.0.1:8080/receive/"
        },
        "module": "hooks.hubotsend.post.sender"
    }
]
```
  and
```
"jsonrpc": [
    {
        "certfile": "/root/server.pem",
        "module": "sinks.hubotreceive.post.receiver",
        "name": "127.0.0.1",
        "port": 8081
    }
]
```

5. On the console in the hubot server, use the following configuration:
```
export HUBOT_HTML_RESPONSE="true"
export HUBOT_REST_SEND_URL="https://127.0.0.1:8081/"
```

6. Restart hubot with `bin/hubot --adapter web`. You will not get a shell
   prompt, the server will just start and become non-interactive.
7. Restart hangupsbot, and ensure that the new sink and hook starts properly.

## Testing

Open your hangout with hangupsbot, and type a standard hubot command
  e.g. `hubot time`, `hubot ping`. After a brief lag, you should see hubot's
  responses in the hangout.

---

HangupsBot (original README.md)
===============================

Bot for Google Hangouts

Requirements
------------

- Python >= 3.3
- hangups (https://github.com/tdryer/hangups)
- appdirs (https://github.com/ActiveState/appdirs)
- asyncio (https://pypi.python.org/pypi/asyncio) for Python < 3.4
- BeautifulSoup4

Usage
-----

Run `hangupsbot --help` to see all available options.
Start HangupsBot by running `hangupsbot`.

You can configure basic settings in `config.json` file. This file will be
copied to user data directory (e.g. `~/.local/share/hangupsbot/` on Linux)
after first start of HangupsBot.

The first time you start HangupsBot, you will be prompted to log into your
Google account. Your credentials will only be sent to Google, and only
session cookies will be stored locally. If you have trouble logging in,
try logging in through a browser first.

Help
----

    usage: hangupsbot [-h] [-d] [--log LOG] [--cookies COOKIES] [--config CONFIG]

    optional arguments:
      -h, --help         show this help message and exit
      -d, --debug        log detailed debugging messages (default: False)
      --log LOG          log file path (default:
                         ~/.local/share/hangupsbot/hangupsbot.log)
      --cookies COOKIES  cookie storage path (default:
                         ~/.local/share/hangupsbot/cookies.json)
      --config CONFIG    config storage path (default:
                         ~/.local/share/hangupsbot/config.json)
