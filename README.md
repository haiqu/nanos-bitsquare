# Bitsquare Ledger app

Proof of concept application that will eventually allow you to create and log into your Bitsquare wallet using the Ledger Nano S.

### Get Started

You will need a Ledger Nano S.

### Install Ledger dev env

See WINDOWS.md

You can also check this: http://ledger.readthedocs.io/en/latest/nanos/setup.html


### Load to Nano S from Python Virtual Environment

```
python -m ledgerblue.loadApp --targetId 0x31100002 --apdu --fileName ./bin/token.hex --appName Bitsquare  --appFlags 0x00 --icon "0100ffffff00000000000000006006f00ff00f700fee76ffffffff6e77f00ef00ff00f600600000000"
```
