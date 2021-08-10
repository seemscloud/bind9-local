`named.conf.options`
```bash
forwarders {
  8.8.8.8;
  8.8.4.4;
};

recursion no;

listen-on {
  any;
};

allow-query { any; };
```

`named.conf`
```bash
include "/etc/bind/named.conf.custom-zones";
```

`named.conf.custom-zones`
```bash
zone "localdomain" {
  type master;
  file "/etc/bind/db.localdomain";
};

zone "10.10.in-addr.arpa" {
  type master;
  file "/etc/bind/db.10.10";
};
```


`db.localdomain`
```bash
$TTL    604800
@       IN      SOA   1dns0.localdomain.  root.1dns0.localdomain. (
                  1   ; Serial
             604800   ; Refresh
              86400   ; Retry
            2419200   ; Expire
             604800 ) ; Negative Cache TTL

@       IN      NS    1dns0.localdomain.
@       IN      A     10.10.10.10

1dns0   IN      A     10.10.10.10

1ucd0   IN      A     10.10.190.10
1ucd1   IN      A     10.10.190.11
1ucd2   IN      A     10.10.190.12
1ucd3   IN      A     10.10.190.12
```

`db.10.10`
```bash
$TTL    604800
@       IN      SOA   1dns0.localdomain. root.1dns0.localdomain. (
                  1   ; Serial
             604800   ; Refresh
              86400   ; Retry
            2419200   ; Expire
             604800 ) ; Negative Cache TTL

@       IN      NS    1dns0.localdomain.
1dns0   IN      A     10.10.10.10

10.10   IN      PTR   1dns0.localdomain.

190.10  IN      PTR   1ucd0.localdomain.
190.11  IN      PTR   1ucd1.localdomain.
190.12  IN      PTR   1ucd2.localdomain.
190.13  IN      PTR   1ucd3.localdomain.
```
