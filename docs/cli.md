# CLI Commands

The following sections describe the CLI commands available to bot developers. You can always use `forta-bot help` to get a quick overview of these commands, as well as details about specific commands e.g. `forta-bot run help`. The command `forta-agent` is still supported and can be used interchangeably with `forta-bot`. 

## Invoke commands programmatically

You can invoke the available CLI commands programmatically in NodeJS using the exported `configureContainer` method. This method returns a dependency injection container that can be used to access different commands. It accepts an optional object of arguments that will configure its behaviour, the most important of which is `contextPath` which points to the bot directory that you are running commands for (defaults to current working directory if not specified). For example, if you wanted to invoke the `init` command it would look like the following:

```js
const { configureContainer } = require("forta-agent");

const pathToBotDirectory = "/Desktop/my-bot";
const container = configureContainer({ contextPath: pathToBotDirectory });
const init = container.resolve("init");
await init();
```

The above code snippet will initialize a Forta bot at the specified `contextPath` (creating the folder if it does not exist). All the CLI commands are asynchronous so you will need to `await` them. Similarly, you can resolve other commands that are listed below.

Exposing the CLI functionality enables developers to integrate with other tools. The first example of such an integration is the [Forta Hardhat plugin](hardhat.md). We look forward to seeing the new and creative ways developers will integrate this functionality into their own toolset!

## init

Using the `npx forta-agent@latest init` command, you can quickly initialize a Forta bot Javascript project inside of the current working directory. The starter project includes some default configuration files as well as an example bot implementation. A keyfile and forta.config.json file will also be generated for you and placed in the ~/.forta folder if they do not already exist. You will be prompted to enter a password that will be used to encrypt the keyfile. This keyfile will be used later when publishing your bot.

Options:

```
--typescript - initialize Typescript project
--python - initialize Python project
```

Example: Initialize a Typescript Project

```
$ npx forta-agent@latest init --typescript
```

## run

Easily verify the behaviour of your bot during local development using the `forta-bot run` command. The default behaviour (i.e. without any options) is to subscribe to a JSON-RPC endpoint and listen for the latest blocks and transactions. A stream of the latest data will be passed to your bot with any findings printed to your output console. The endpoint is specified by the `jsonRpcUrl` property in the forta.config.json file.

Options:

```
--tx - run your bot with a specific transaction hash
--block - run your bot against a specific block number/hash, including the transactions in the block
--range - run your bot against a specific range of block numbers
--file - run your bot against a JSON file of test data
--prod - used for running the bot inside of a production environment i.e. you probably won’t need this during development
--config - specify a config file to use (default: forta.config.json)
--nocache - disables writing block/tx data to local disk cache (but reads are still enabled)
```

Example: Run for a specific transaction

```bash
$ forta-bot run --tx 0xf9c43e15ef2abfec163ec3b1165f18a5119ba119b6e059fc924903e5251e3543
```

or if using locally installed package

```bash
$ npm run tx 0xf9c43e15ef2abfec163ec3b1165f18a5119ba119b6e059fc924903e5251e3543
```

Example: Run for a specific block (by number)

```
$ forta-bot run --block 12821978
```

or if using locally installed package

```bash
$ npm run block 12821978
```

Example: Run for a specific block (by hash)

```
$ forta-bot run --block 0x9e052eb02a3849b650e8b9e0a47b1fae194b928c930168ef19e311dbd7886172
```

or if using locally installed package

```bash
$ npm run block 0x9e052eb02a3849b650e8b9e0a47b1fae194b928c930168ef19e311dbd7886172
```

Example: Run for a specific block range

```
$ forta-bot run --range 12821978..12821980
```

or if using locally installed package

```bash
$ npm run range 12821978..12821980
```

Example: Run for an input file

```
$ forta-bot run --file ./test.data.json
```

or if using locally installed package

```bash
$ npm run file ./test.data.json
```

## publish

Deploy your bot to the Forta network using the `forta-bot publish` command. This will build a Docker image for your bot and publish it to the image repository. Also, it will create a bot manifest which includes the Docker image reference, sign the manifest using your keyfile (you will be prompted for password), store it on IPFS and publish the IPFS reference to the Bot Registry contract.

Options:

```
--config - specify a config file to use (default: forta.config.json)
```

## push

Push your bot image to a repository where scan nodes can find it. This will build and push your Docker bot image (but will not add it to the bot registry). Intended to be used as part of the Forta App bot deployment flow.

Options:

```
--config - specify a config file to use (default: forta.config.json)
```

## logs

Retrieves logs for specified bot id and prints them to the console. The default `botId` is read from `forta.config.json` unless specified.

Options (optional):

```
--botId - Bot id to retrieve information for. Default value is this bot. If this option is used do not provide a agentId

--agentId - Bot id to retrieve information for. Default value is this bot. If this option is used do not provide a botId

--before - An ISO timestamp [YYYY-MM-DDTHH:mmZ] representing the latest time to include in logs

--after - An ISO timestamp [YYYY-MM-DDTHH:mmZ] representing the oldest time to include in logs

--scannerId - Filter to only return logs of a given scannerId
```


## info

Inspect the state of your bot. This command prints the bot's IPFS metadata to the console as well as recent bot events. The default `botId` is read from `forta.config.json` unless specified.

Bot events include:

- Bot Created
- Bot Enabled/Disabled
- Bot Updated

Options (optional):

```
--botId - Bot id to retrieve information for. Default value is this bot. If this option is used do not provide a agentId

--agentId - Bot id to retrieve information for. Default value is this bot. If this option is used do not provide a botId
```

## disable

Disable your deployed bot and stop it from running on the Forta network by using `forta-bot disable`. Your Docker image will still be publicly available, but scan nodes will know not to run your bot.

## enable

Enable your bot and start execution on the Forta network by using `forta-bot enable`. By default, your bot is enabled when you deploy to the Forta network. Only use this command if your bot is currently disabled.

## keyfile

You can print out information about your keyfile, including its absolute path and address, using the `forta-bot keyfile` command.
