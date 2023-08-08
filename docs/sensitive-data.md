# Protecting sensitive data

There are cases where developers need to use sensitive information in their bot (e.g. some API key) or just hide their bot code from the public. While Forta does not currently support storage of secrets (since all bot images are stored in a public repository), developers can still use JWT authentication or code obfuscation as two ways to protect sensitive data.

It should be noted that obfuscation is not the same as encryption, and that obfuscation can potentially be reversed with enough effort. With this in mind, we do **not** recommend storing high-value secrets in your bots. Instead, you can use the pattern for [JWT authentication for bots](jwt-auth.md) to securely load secrets without storing them in the code.

This page will demonstrate how to obfuscate your code using an example Javascript bot. You can find the code for this example [here](https://github.com/forta-network/forta-bot-examples/tree/master/hiding-sensitive-data-js).

## Obfuscating code

In this example, the [javascript-obfuscator](https://github.com/javascript-obfuscator/javascript-obfuscator) library is used to obfuscate the bot code, but you can use any library you prefer. An npm script is added to the project to run the obfuscator tool over the code: `npm run obfuscate`. The script in package.json will look like:

```
"scripts": {
  ...
  "obfuscate": "javascript-obfuscator ./src --output ./obfuscated --config obfuscation-config.js"
}
```

This will take all of the Javascript files in the src folder and output obfuscated versions of each file with the same name to the obfuscated folder (if using Typescript, you should obfuscate the compiled Javascript files in the dist folder instead of the src folder). The npm script simply invokes the `javascript-obfuscator` tool with some obfuscation options stored in obfuscation-config.js.

It is recommended to obfuscate _before_ building your bot image so that you can verify the results of the obfuscation and make sure it meets your expectations. You can also try running the obfuscated code to verify that it still works by moving the obfuscated files over to the src folder. Please note that the `javascript-obfuscator` tool can output different results based on the same settings, so be sure to verify that the obfuscated result is good enough for you.

If there are any json files in your bot (e.g. ABI.json), consider converting them into Javascript (.js) files so that they also get obfuscated and don't reveal anything about the bot. Make sure that unit tests are also obfuscated, or better yet, just not included in the final image. This could easily reveal what the bot is doing.

## Obfuscation settings

The obfuscation-config.js contains a number of settings for manipulating the code. You may want to tweak these settings in order to further obfuscate your code. There are a few [preset options](https://github.com/javascript-obfuscator/javascript-obfuscator#preset-options) you can experiment with to achieve your desired level of obfuscation. Keep in mind that there will be a tradeoff between obfuscation and performance when tweaking these settings.

Be careful if tweaking the obfuscation-config.js settings, as some of the options could potentially break your code. For example, the `selfDefending` option will prevent your code from running if it is formatted in any way after being obfuscated. See the [complete list of options](https://github.com/javascript-obfuscator/javascript-obfuscator#javascript-obfuscator-options) to get a better understanding.

## Updating the Dockerfile

The Dockerfile in the example is slightly modified to copy the obfuscated source code from the obfuscated folder instead of the src folder:

```
...
# copy code over from obfuscated folder
COPY /obfuscated ./src
...
```

This will ensure only the obfuscated code gets published in the bot image.

Awesome! You now have an obfuscated bot that can store sensitive data in a publicly available image.
