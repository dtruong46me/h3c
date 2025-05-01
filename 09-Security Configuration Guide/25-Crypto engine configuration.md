
# Configuring crypto engines

## About crypto engines

Crypto engines encrypt and decrypt data for
service modules.

The device supports only one software
crypto engine, which is a set of software encryption algorithms. The software
crypto engine is always enabled.

When a service module requires data
encryption/decryption, it sends the desired data to the crypto engine. After
the crypto engine completes data encryption/decryption, it sends the data back
to the service module. 

## Display and maintenance commands for crypto engines

Execute display
commands in any view and reset commands in user
view.

 

| Task | Command |
| --- | --- |
| Display crypto engine information. | display crypto-engine || Display crypto engine statistics. | display crypto-engine statistics \[ engine-id engine\-id slot slot-number ] || Clear crypto engine statistics. | reset crypto-engine statistics \[ engine-id engine\-id slot slot-number ] |



‌

