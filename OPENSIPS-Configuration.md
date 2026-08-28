# Configuration

## Pre-requisites
### Create a domain
1. Configuration dans opensips.cfg
```text
loadmodule "domain.so"
modparam("domain", "db_url", "mysql://opensips:opensips_rw@localhost/opensips")
modparam("domain", "db_mode", 1)   # 0 = cache only, 1 = use DB
```
2. Add route logic to validate domain
```text
route {
    # Reject requests for domains we don't handle
    if (!is_domain_local("$rd")) {
        sl_send_reply("404", "Not Here");
        exit;
    }

    if (is_method("REGISTER")) {
        if (!www_authorize("$td", "subscriber")) {
            www_challenge("$td", "0");
            exit;
        }
        if (!save("location")) {
            sl_reply_error();
        }
        exit;
    }

    # ... rest of routing ...
}
```

### Users
#### OpenSips
1. The module responsible for user authentication/authorization:
    - Server side
```text
#### Digest authentication against the database
loadmodule "auth.so"
loadmodule "auth_db.so"
modparam("auth_db", "db_url", "postgres://opensips:9PNTuUGKVs5DPCZ6@localhost/opensips")
modparam("auth_db", "calculate_ha1", no)
modparam("auth_db", "password_column", "ha1")
modparam("auth_db", "password_column_2", "ha1b")
modparam("auth_db", "load_credentials", "$avp(ha1)=ha1;$avp(ha1b)=ha1b")
modparam("auth_db", "use_domain", 1)
modparam("auth", "username_spec", "$avp(username)")
modparam("auth", "password_spec", "$avp(password)")
modparam("auth", "calculate_ha1", no)
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