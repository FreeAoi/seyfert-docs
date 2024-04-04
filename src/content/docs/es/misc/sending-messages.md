---
title: Sending Messages
---
 
The basic feature of the Discord Bots is to send messages all around Discord. And in Seyfert you can send them in the easiest way.
 
First of all, we have to setup a basic `Hello World` command.
 
```ts title="src/commands/helloworld.ts" showLineNumbers
import {
	Command,
	Declare
} from "seyfert";
 
@Declare({
	name: "helloworld",
	description: "Send a basic hello world message."
})
export default class HelloWorldCommand extends Command {
	async run(ctx: CommandContext) {}
}
```
 
Having set up our basic `Hello World` command we are ready to send our first message using `CommandContext.write()` function.
 
```ts title="src/commands/helloworld.ts" ins={12} showLineNumbers
import {
	Command,
	Declare
} from "seyfert";
 
@Declare({
	name: "helloworld",
	description: "Send a basic hello world message."
})
export default class HelloWorldCommand extends Command {
    async run(ctx: CommandContext) {
        ctx.write({ content: "Hello World 👋" });
    }
}
```
 
The `CommandContext.write()` function will respond to the command.
 
## EditOrReply
 
But, what about responding to the command or editing it's reply instead of simply replying to it?
 
We can use `CommandContext.editOrReply()` function. This function is used to reply the command or, if the response has been sent, edit it. 
 
This function is very useful if we want to develop a command which responds to the command or if the command was responded the response will be edited. If we are only using a bare `CommandContext.write()` we will send a reply in all the cases.
 
Here is an example of how implement this function. 
 
```ts title="src/commands/helloworld.ts" ins={3,7} showLineNumbers
export default class HelloWorldCommand extends Command {
	async run(ctx: CommandContext) {
				ctx.deferReply();

			 // do something that takes time and is boring

        ctx.editOrReply({ content: "I made things" });
    }
}
```
 
## Sending messages without a reply
 
Reading this guide you could have thought about the possibility of only send a message to a channel instead of replying to the command.
 
Here we are. To send a simple message to a specific channel we need to retrive it's id and then access to `BaseClient.messages` property and run the `write` function.
 
Here is an example of how to send that message without replying a command:
 
```ts title="src/commands/helloworld.ts" ins={3} showLineNumbers
export default class HelloWorldCommand extends Command {
    async run(ctx: CommandContext) {
        ctx.client.messages.write(ctx.channelId, { content: "Hello World 👋" });
    }
}
```
 
## Sending Embeds
 
Discord adds the possibility to send embeded messages within a channel. 
 
To send those embeded messages with Seyfert we will have to build up the embed with the Embed builder. For further information about the cusomization of the embeded message you can check the [Embed builder](/api/classes/embed) within this docs.
 
Here is an example of how to send an embed with a custom title and description.
 
```ts title="src/commands/helloworld.ts" {1} {"1. Ah yes, builders.":6-9} ins={11} showLineNumbers
 
import { Embed } from "seyfert"
 
export default class HelloWorldCommand extends Command {
	async run(ctx: CommandContext) {
 

		const embed = new Embed()
		.setTitle("My Awesome Embed")
		.setDescription("Hello World 👋")
 
    ctx.write({ embeds: [embed] });
  }
}
```
 
## Sending components attached to the message
 
Discord includes the possibility to send components attached to the message within an `ActionRow`. Those components can be [`Buttons`](/api/classes/button) or [`SelectMenus`](/api/classes/selectmenu/).
 
The components are stored into an [`ActionRow`](/api/classes/actionrow) which can contain up to 5 diffrent buttons and only one select menu and can't contain inside another ActionRow.
 
In this example we are going to send two actions rows within the message. Each row is going to have a button and a [string select menu](/api/classes/stringselectmenu) attached respectively.
 
```ts title="src/commands/helloworld.ts" ins={1-7} {"1. Build button": 12-19} {"2. Build selectmenu": 21-29} ins={31} showLineNumbers
import { 
	ActionRow,
	Button, 
	StringSelectMenu,
	ButtonStyle,
	StringSelectOption
} from "seyfert";
 
export default class HelloWorldCommand extends Command {
	async run(ctx: CommandContext) {
 

		const button = new Button()
		.setCustomId("helloworld")
		.setLabel("Hello World")
		.setStyle(ButtonStyle.Primary)
 
		const buttonRow = new ActionRow<Button>()
		.addComponents(button)
 

		const menu = new StringSelectMenu()
		.setCustomId("select-helloworld")
		.addOption(
			new StringSelectOption().setLabel("Hello").setValue("option_1")
		)

		const menuRow = new ActionRow<StringSelectMenu>()
		.addComponents(menu) 
 
    ctx.write({ content: "Hello World 👋", components: [buttonRow, menuRow] });
  }
}
```

:::note

For further information about components see the [components guide](../components/introduction).
:::