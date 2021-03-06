# btp-toolbox
Tools for building and debugging with the Bilateral Transport Protocol (BTP)

# <img src="./assets/cat.svg" width="25px"> Cat

BtpCat is used strictly for logging.
It can be used to display BTP packets, and the ILP packets inside them,
in a nice human-readable way. It's a single function that takes a JavaScript object, and
outputs a String so that `eval(BtpCat(obj)) === obj`. Example usage:

```js
const BtpPacket = require('btp-packet')
const IlpPacket = require('ilp-packet')
const BtpCat = require('./src/cat')
const chalk = require('chalk')

console.log(chalk.bold.green('alice sends:'), chalk.green(BtpCat({
  type: BtpPacket.TYPE_MESSAGE,
  requestId: 4,
  data: {
    protocolData: [
      {
        protocolName: 'ilp',
        contentType: BtpPacket.MIME_APPLICATION_OCTET_STRING,
        data: IlpPacket.serializeIlpPacket({
          "type": 6,
          "typeString": "ilqp_by_destination_request",
          "data": {
            "destinationAccount": "de.eur.blue.bob",
            "destinationAmount": "9000000000",
            "destinationHoldDuration": 3000
          }
        })
      }, {
        protocolName: 'to',
        contentType: BtpPacket.MIME_TEXT_PLAIN_UTF8,
        data: 'us.usd.red.connie'
      }
    ]
  }
}, BtpPacket.BTP_VERSION_1)))

console.log(chalk.bold.red('alice receives:', BtpCat({
  type: BtpPacket.TYPE_RESPONSE,
  requestId: 4,
  data: {
    protocolData: [
      {
        protocolName: 'from',
        contentType: BtpPacket.MIME_TEXT_PLAIN_UTF8,
        data: 'us.usd.red.connie'
      }, {
        protocolName: 'to',
        contentType: BtpPacket.MIME_TEXT_PLAIN_UTF8,
        data: 'us.usd.red.alice'
      }, {
        protocolName: 'ilp',
        contentType: BtpPacket.MIME_APPLICATION_OCTET_STRING,
        data: IlpPacket.serializeIlpPacket({
          "type": 7,
          "typeString": "ilqp_by_destination_response",
          "data": {
            "sourceAmount": "10782788022",
            "sourceHoldDuration": 5000
          }
        })
      }
    ]
  }
}, BtpPacket.BTP_VERSION_1)))
```

Example output:

<img src="./assets/screenshot.png" width="500px">
