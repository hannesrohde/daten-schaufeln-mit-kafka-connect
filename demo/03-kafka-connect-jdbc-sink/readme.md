# Scenario 3 - publishing data on telegram

In this scenario, a telegram sink connector is used to publish data from a kafka topic to telegram.

We reuse the source connector and topic from scenario 2, please follow the steps there to set up.

## Create a telegram bot and group

Using your telegram client, start a conversation with the @BotFather and create a new bot as described [here](https://github.com/fbascheper/kafka-connect-telegram)

    /newbot

    > BotFather, [02.12.20 20:35]
    > Alright, a new bot. How are we going to call it? Please choose a name for your bot.

    logfilesBot

    > BotFather, [02.12.20 20:36]
    > Good. Now let's choose a username for your bot.
    > It must end in `bot`. Like this, for example:
    > TetrisBot or tetris_bot.

    logfilesBot

    > BotFather, [02.12.20 20:36]
    > Done! Congratulations on your new bot. You will find it at t.me/logfilesBot.
    > You can now add a description, about section and profile picture for your bot, see /help for a list of commands.
    > By the way, when you've finished creating your cool bot, ping our Bot Support if you want a better username for it.
    > Just make sure the bot is fully operational before you do this.
    > Use this token to access the HTTP API:
    > 1344941351:AAEmPOnZTWWTVDAddn-vMINWPE5VZP0cfU0
    > Keep your token secure and store it safely, it can be used by anyone to control your bot.
    > For a description of the Bot API, see this page: https://core.telegram.org/bots/api

Next, create a group "logfiles" in your telegram client and add @logfilesBot to it.
Send a test message and then determine the group chat id:

    curl https://api.telegram.org/bot1344941351:AAEmPOnZTWWTVDAddn-vMINWPE5VZP0cfU0/getUpdates

The json response should contain the chat id:

    {
        "ok":true,
        "result":[
            {
                "update_id":756181107,
                "message":{
                    "message_id":4,
                    "from":{
                        "id":974935068,
                        "is_bot":false,
                        "first_name":"Hannes"
                    },
                    "chat":{
                        "id":-410243989,
                        "title":"logfiles",
                        "type":"group",
                        "all_members_are_administrators":true
                    },
                    "date":1606938782,
                    "text":"/test",
                    "entities":[
                        {
                            "offset":0,
                            "length":5,
                            "type":"bot_command"
                        }
                    ]
                }
            }
        ]
    }

## Deploy sink connector

    kubectl apply -f 03-kafka-sink-connector.yaml


## Cleanup

Delete all resources we created for scenario 2 and 3:

    kubectl delete all,kafkatopic,kafkaconnector -l scenario=1
    kubectl delete all,kafkatopic,kafkaconnector -l scenario=2
    kubectl delete all,kafkatopic,kafkaconnector -l scenario=3
