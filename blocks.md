# Blocks

Blocks are the building base of our database. Outlined below is their structure

## Structure

The structure was briefly covered in the [README.md](/README.md)

As stated, block headers should consist of a timestamp, a nonce, a target, and a domain name.

### Block structure

| Field | Description |
| -------------- | --------------- |
| blockheader | blockheaders |
| hash | signed hash of blockheaders |
| hashRecord | signed hash of all records, does not go through Proof-of-Work |
| public key | key used to verify signed hash |

There are additional fields for records, such as A records, but these are optional. If no record is provided, an A record field is created with the value of 1.

### Blockheaders

| Field   | Description    |
|--------------- | --------------- |
| timestamp   | Unix timestamp since 1970-01-01T00:00 UTC   |
| nonce   | nonce from Proof-of-Work   |
| target   | target from Proof-of-Work   |
| domain name | name of the domain like `example.org` |

## Database

Blocks are stored in an lmdb database with their key being the hash (signed hash of blockheaders not hashRecord).
