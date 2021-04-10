# golang-auth

## Set Up MySQL Database with docker

```bash
# start database
docker run --name dev-mysql -e MYSQL_ROOT_PASSWORD=<password> -d -p 3306:3306 mysql:latest
# connect to database
docker exec -it dev-mysql bash
mysql -u root -p
```

## Set Up PostgreSQL Database with docker

```bash
docker run --name wol-db \
-e POSTGRES_PASSWORD=password \
-e PGDATA=/var/lib/postgresql/data/pgdata \
-v $PWD/pgdata:/var/lib/postgresql/data \
-d -p 5432:5432 \
postgres:latest


```

```
PORT=...
DB_USERNAME=...
DB_PASSWORD=...
DB_DATABASE=...
JWS_SECRET=...
JWT_VALID_TIME=...
```

### MySQL Schema

```sql
create table if not exists users
(
    id       bigint unsigned auto_increment
        primary key,
    username varchar(255) null,
    password longblob     null,
    constraint username
        unique (username)
);

create table if not exists computers
(
    id      bigint unsigned auto_increment
        primary key,
    user_id bigint unsigned                        not null,
    name    varchar(255)                           not null,
    mac     varchar(18)                            not null,
    ip      varchar(26)  default '255.255.255.255' not null,
    port    int unsigned default '9'               not null,
    constraint macs_pk
        unique (user_id, name),
    constraint macs_users_id_fk
        foreign key (user_id) references users (id)
            on update cascade on delete cascade
);
```

### Postgresql Schema

```sql
create table if not exists users
(
    id       bigserial not null
        constraint users_pkey
            primary key,
    username text
        constraint users_username_key
            unique,
    password bytea
);

alter table users
    owner to postgres;


create table computers
(
    id      serial                                                    not null
        constraint computers_pk
            primary key,
    user_id integer                                                   not null
        constraint computers_users_id_fk
            references users
            on update cascade on delete cascade,
    name    varchar(255)                                              not null,
    mac     varchar(17)                                               not null,
    ip      varchar(100) default '255.255.255.255'::character varying not null,
    port    integer      default 9                                    not null
);

alter table computers
    owner to postgres;
```