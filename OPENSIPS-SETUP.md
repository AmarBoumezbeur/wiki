# Installation
## Opensips
1. Build essentials 
```text
sudo apt update && sudo apt upgrade -y
sudo apt install -y gnupg2 wget curl build-essential
```

2. Check openSips version
```text
curl -s https://apt.opensips.org/dists/ | grep -i bookworm
```

3. Bookworm release is available
* Install GPG key
```text
curl -fsSL https://apt.opensips.org/pubkey.gpg | gpg --dearmor -o /usr/share/keyrings/opensips.gpg
```

4. Add APT debian release and update APT
```text
echo "deb [signed-by=/usr/share/keyrings/opensips.gpg] https://apt.opensips.org bookworm 3.6-releases" \
  > /etc/apt/sources.list.d/opensips.list

apt update
```

5. Install Opensips
```text
sudo apt install -y opensips opensips-mysql-module opensips-cli opensips-postgres-module opensips-auth-modules opensips-postgres-dbschema
```
    - Modules
        - SIP routing/Events/Interfaces: Opensips Core module 
        - User authentication/Authorization: OpenSips POstgresql/Schema Module and Authentication Module
        - CLI: OpenSips CLI module
6. Check if opensips is UP
```text
systemctl statu opensips
````
7. Opensips-cli 
* [Database Module](https://github.com/OpenSIPS/opensips-cli/blob/master/docs/modules/database.md#setting-up-the-database-module)
8. Configure /etc/opensips/opensips-cli.cfg
```text
[default]
log_level:            INFO
prompt_name:          opensips-cli
prompt_intro:         Welcome to OpenSIPS Command Line Interface!
history_file:         ~/.opensips-cli.history
output_type:          pretty-print
communication_type:   fifo
fifo_file:            /tmp/opensips_fifo

database_schema_path: /usr/share/opensips
#database_schema_path: /usr/local/share/opensips/postgres
database_url:         postgresql://opensips:9PNTuUGKVs5DPCZ6@localhost/opensips
database_admin_url:   postgresql://postgres@localhost
database_name:        opensips
database_modules:     auth_db usrloc
```

## Database

1. Install postgresql
```text
sudo apt install -y postgresql-15 postgresql-contrib python3-psycopg2
```


2. Initialize Opensips Database:
- Postgresql
    - Alter postgres user role 
```text
sudo -u postgres psql -c "ALTER USER postgres WITH PASSWORD '9PNTuUGKVs5DPCZ6';"
```
    - Create postgresql user opensips (optional)
```text
systemctl enable --now postgresql
su - postgres -c "psql -c \"CREATE ROLE opensips WITH LOGIN PASSWORD '9PNTuUGKVs5DPCZ6' SUPERUSER;\""
```

3. Initialize the DB (Make sure the opensips-cli.cg and postgresql schemas exists )
```text
opensips-cli -x database create
```

4. Connect to the DB:
```text
psql -U postgres -h localhost
psql -U opensips -h localhost
```

## Configuration
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
```

#### Database
1. Tables Schemas
[Postresql Schemas](https://docs.opensips.org/manual/3-6/install-dbschema/)
2. The table where all users are going to be configured is 'subscriber'
```text
opensips=# \d subscriber
                                       Table "public.subscriber"
     Column     |         Type          | Collation | Nullable |                Default                 
----------------+-----------------------+-----------+----------+----------------------------------------
 id             | integer               |           | not null | nextval('subscriber_id_seq'::regclass)
 username       | character varying(64) |           | not null | ''::character varying
 domain         | character varying(64) |           | not null | ''::character varying
 password       | character varying(25) |           | not null | ''::character varying
 ha1            | character varying(64) |           | not null | ''::character varying
 ha1_sha256     | character varying(64) |           | not null | ''::character varying
 ha1_sha512t256 | character varying(64) |           | not null | ''::character varying
Indexes:
    "subscriber_pkey" PRIMARY KEY, btree (id)
    "subscriber_account_idx" UNIQUE CONSTRAINT, btree (username, domain)
    "subscriber_username_idx" btree (username)
```
