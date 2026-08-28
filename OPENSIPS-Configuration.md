# Configuration

## Pre-requisites
### Users
#### OpenSips
1. The module responsible for user authentication/authorization:
    - Server side
```text
#### Digest authentication against the database
loadmodule "auth.so"
loadmodule "auth_db.so"
modparam("auth_db", "db_url", "postgres://opensips:9PNTuUGKVs5DPCZ6@localhost/opensips")
```
    - Client
```text
#### UAC
loadmodule "uac_auth.so"
loadmodule "uac.so"
# UAC restore mode is set to auto for easier From transformation between SIP and TEL URIs
modparam("uac", "restore_mode", "auto")
2. The database needs to be created

## Add users
1. Create a user
```text
opensips-cli -x subscriber add username=amar password=AKzlahqscnjdsbf domain=your-domain.com
```
2. 