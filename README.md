# Telegram Forwarder

A simple Telegram Python bot running on Python3 to automatically forward messages from one chat to another.

## Starting The Bot

Once you've setup your your configuration (see below) is complete, simply run:

```shell
python -m forwarder
```

or with poetry (recommended)

```shell
poetry run forwarder
```

## Setting Up The Bot (Read the instruction bellow before starting the bot!):

Telegram Forwarder only supports Python 3.9 and higher.

### Configuration

There are two files mandatory for the bot to work `.env` and `chat_list.json`.

#### `.env`

Template env may be found in `sample.env`. Rename it to `.env` and fill in the values:

- `BOT_TOKEN` - Telegram bot token. You can get it from [@BotFather](https://t.me/BotFather)

- `OWNER_ID` - An integer of consisting of your owner ID.

- `REMOVE_TAG` - set to `True` if you want to remove the tag ("Forwarded from xxxxx") from the forwarded message.

#### `chat_list.json`

Template chat_list may be found in `chat_list.sample.json`. Rename it to `chat_list.json`.

This file contains the list of chats to forward messages from and to. The bot expect it to be an Array of objects with the following structure:

```json
[
  {
    "source": -10012345678,
    "destination": [-10011111111, "-10022222222#123456"]
  },
  {
    "source": "-10087654321#000000", // Topic/Forum group
    "destination": ["-10033333333#654321"],
    "filters": ["word1", "word2"] // message that contain this word will be forwarded
  },
  {
    "source": -10087654321,
    "destination": [-10033333333],
    "blacklist": ["word3", "word4"] // message that contain this word will not be forwarded
  },
  {
    "source": -10087654321,
    "destination": [-10033333333],
    "filters": ["word5"],
    "blacklist": ["word6"]
    // message must contain word5 and must not contain word6 to be forwarded
  }
]
```

- `source` - The chat ID of the chat to forward messages from. It can be a group or a channel.

  > If the source chat is a Topic groups, you **MUST** explicitly specify the topic ID. The bot will ignore incoming message from topic group if the topic ID is not specified.

- `destination` - An array of chat IDs to forward messages to. It can be a group or a channel.

  > Destenation supports Topics chat. You can use `#topicID` string to forward to specific topic. Example: `[-10011111111, "-10022222222#123456"]`. With this config it will forward to chat `-10022222222` with topic `123456` and to chat `-10011111111` .

- `filters` (Optional) - An array of strings to filter words. If the message containes any of the strings in the array, it **WILL BE** forwarded.

- `blacklist` (Optional) - An array of strings to blacklist words. If the message containes any of the string in the array, it will **NOT BE** forwarded.

You may add as many objects as you want. The bot will forward messages from all the chats in the `source` field to all the chats in the `destination` field. Duplicates are allowed as it already handled by the bot.

### Python dependencies

Install the necessary python dependencies by moving to the project directory and running:

```shell
poetry install --only main
```

or with pip

```shell
pip3 install -r requirements.txt
```

This will install all necessary python packages.

### Launch in Docker container

#### Requrements

- Docker
- docker compose

Before launch make sure all configuration are completed (`.env` and `chat_list.json`)!

Then, simply run the command:

```shell
docker compose up -d
```

You can view the logs by the command:

```shell
docker compose logs -f
```

## How to get Telegram Bot Chat ID

Original source can be found here: https://gist.github.com/nafiesl/4ad622f344cd1dc3bb1ecbe468ff9f8a


## Create a Telegram Bot and get a Bot Token

1. Open Telegram application then search for `@BotFather`
1. Click Start
1. Click Menu -> /newbot or type `/newbot` and hit Send
1. Follow the instruction until we get message like so
    ```
    Done! Congratulations on your new bot. You will find it at t.me/new_bot.
    You can now add a description.....

    Use this token to access the HTTP API:
    63xxxxxx71:AAFoxxxxn0hwA-2TVSxxxNf4c
    Keep your token secure and store it safely, it can be used by anyone to control your bot.

    For a description of the Bot API, see this page: https://core.telegram.org/bots/api
    ```
1. So here is our bot token `63xxxxxx71:AAFoxxxxn0hwA-2TVSxxxNf4c` (make sure we don't share it to anyone).


## Get Chat ID for a Private Chat

1. Search and open our new Telegram bot
1. Click Start or send a message
1. Open this URL in a browser `https://api.telegram.org/bot{our_bot_token}/getUpdates`   
    - See we need to prefix our token with a word `bot`
    - Eg: `https://api.telegram.org/bot63xxxxxx71:AAFoxxxxn0hwA-2TVSxxxNf4c/getUpdates`
1. We will see a json like so
    ```
    {
      "ok": true,
      "result": [
        {
          "update_id": 83xxxxx35,
          "message": {
            "message_id": 2643,
            "from": {...},
            "chat": {
              "id": 21xxxxx38,
              "first_name": "...",
              "last_name": "...",
              "username": "@username",
              "type": "private"
            },
            "date": 1703062972,
            "text": "/start"
          }
        }
      ]
    }
    ```
1. Check the value of `result.0.message.chat.id`, and here is our Chat ID: `21xxxxx38`
3. Let's try to send a message: `https://api.telegram.org/bot63xxxxxx71:AAFoxxxxn0hwA-2TVSxxxNf4c/sendMessage?chat_id=21xxxxx38&text=test123`
4. When we set the bot token and chat id correctly, the message `test123` should be arrived on our Telegram bot chat.


## Get Chat ID for a Channel

1. Add our Telegram bot into a channel
1. Send a message to the channel
1. Open this URL `https://api.telegram.org/bot{our_bot_token}/getUpdates`
1. We will see a json like so
    ```
    {
      "ok": true,
      "result": [
        {
          "update_id": 838xxxx36,
          "channel_post": {...},
            "chat": {
              "id": -1001xxxxxx062,
              "title": "....",
              "type": "channel"
            },
            "date": 1703065989,
            "text": "test"
          }
        }
      ]
    }
    ```
1. Check the value of `result.0.channel_post.chat.id`, and here is our Chat ID: `-1001xxxxxx062`
1. Let's try to send a message: `https://api.telegram.org/bot63xxxxxx71:AAFoxxxxn0hwA-2TVSxxxNf4c/sendMessage?chat_id=-1001xxxxxx062&text=test123`
1. When we set the bot token and chat id correctly, the message `test123` should be arrived on our Telegram channel.


## Get Chat ID for a Group Chat

The easiest way to get a group chat ID is through a Telegram desktop application.

1. Open Telegram in a desktop app
1. Add our Telegram bot into a chat group
1. Send a message to the chat group
1. Right click on the message and click `Copy Message Link`
    - We will get a link like so: `https://t.me/c/194xxxx987/11/13`
    - The pattern: `https://t.me/c/{group_chat_id}/{group_topic_id}/{message_id}`
    - So here is our Chat ID: `194xxxx987`
1. To use the group chat ID in the API, we need to prefix it with the number `-100`, like so: `-100194xxxx987`
1. Now let's try to send a message: `https://api.telegram.org/bot63xxxxxx71:AAFoxxxxn0hwA-2TVSxxxNf4c/sendMessage?chat_id=-100194xxxx987&text=test123`
1. When we set the bot token and chat id correctly, the message `test123` should be arrived on our group chat.


## Get Chat ID for a Topic in a Group Chat

In order to send a message to a specific topic on Telegram group, we need to get the topic ID.

1. Similar to steps above, after we click the `Copy Message Link`, we will get a link like: `https://t.me/c/194xxxx987/11/13`, so the group Topic ID is `11`.
1. Now we can use it like so (see `message_thread_id`): `https://api.telegram.org/bot783114779:AAEuRWDTFD2UQ7agBtFSuhJf2-NmvHN3OPc/sendMessage?chat_id=-100194xxxx987&message_thread_id=11&text=test123`
1. When we set the bot token and chat id correctly, the message `test123` should be arrived inside our group chat topic.


### Credits

- [AutoForwarder-TelegramBot](https://github.com/saksham2410/AutoForwarder-TelegramBot)
