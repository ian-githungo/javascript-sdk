# Node SDK

[![NPM](https://nodei.co/npm/elarian.png?downloads=true&downloadRank=true&stars=true)](https://www.npmjs.org/package/elarian)

> A convenient way to interact with the Elarian APIs.



## Install

You can install the package from [npm](https://www.npmjs.com/package/elarian) by running: 

```bash
$ npm install elarian
```

## Usage

```javascript
// Simple SMS+USSD app
const { Client, Customer }  = require('elarian');

const client = new Client({
    apiKey: 'test_api_key',
    orgId: 'test_org_id',
    appId: 'test_app_id',
});

await client.connect();

client.on('ussdSession', async (data, customer) => {
    const {
        input,
        sessionId,
    } = data;

    const metadata = await customer.leaseMetadata('awesomeSurvey');
    let {
        name,
        state = 'newbie',
    } = metadata.value;

    const menu = {
        text: null,
        isTerminal: true,
    };

    switch (state) {
    case 'veteran':
        if (name) {
            menu.text = `Welcome back ${name}! What is your new name?`;
            menu.isTerminal = false;
        } else {
            name = input.value;
            menu.text = `Thank you for trying Elarian, ${name}!`;
            menu.isTerminal = true;
            await customer.sendMessage(
                { number: 'Elarian', provider: 'telco' },
                { text: `Hey ${name}! Thank you for trying out Elarian` },
            );
        }
        break;
    case 'newbie':
    default:
        menu.text = 'Hey there, welcome to Elarian! What\'s your name?';
        menu.isTerminal = false;
        state = 'veteran';
        break;
    }

    await customer.updateMetadata({
        awesomeSurvey: {
            state,
            name,
        },
    });
    return menu;
});

```

## Documentation

Take a look at the [API docs here](http://docs.elarian.com). For detailed info on this SDK, see the [documentation](doc/Client.html).

## Development

Run all tests:

```bash
$ npm install
$ npm test
```

## Issues

If you find a bug, please file an issue on [our issue tracker on GitHub](https://github.com/ElarianLtd/node-sdk/issues).