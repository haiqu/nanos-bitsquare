# Bitsquare Ledger app

Proof of concept application that will eventually allow you to create and log into your Bitsquare wallet using the Ledger Nano S.

### Get Started

You will need a Ledger Nano S.

### Install Ledger dev env

See WINDOWS.md

You can also check this: http://ledger.readthedocs.io/en/latest/nanos/setup.html


### Load to Nano S from Python Virtual Environment

```
python -m ledgerblue.loadApp --targetId 0x31100002 --apdu --fileName ./bin/token.hex --appName Bitsquare  --appFlags 0x00 --icon "010000000000ffffffffffffffffff1ff11ff18ff177ef111111119199efff1ff11ff19ff9ffffffff"
```

or more simply

`make load`