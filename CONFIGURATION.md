`named.conf.options`
```bash
options {
  directory "/var/cache/bind";

  recursion yes;
  querylog yes;
  auth-nxdomain no;
  dnssec-validation auto;
  
  forwarders {
    8.8.8.8;
  };

  listen-on-v6 port 53 {
    any;
  };

  listen-on port 53 {
    any;
  };

  allow-query {
    any;
  };

  allow-recursion {
    any;
  };

  allow-transfer {
    none;
  };
};
```

`named.conf`
```bash
include "/etc/bind/named.conf.custom-zones";
include "/etc/bind/named.conf.logging";
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

`/etc/bind/named.conf.logging`
```bash
logging {
  channel bind_log {
    file "/var/log/bind/bind.log" versions 3 size 5m;
    severity info;
    print-category yes;
    print-severity yes;
    print-time yes;
  };
  category default { bind_log; };
  category update { bind_log; };
  category update-security { bind_log; };
  category security { bind_log; };
  category queries { bind_log; };
  category lame-servers { null; };
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
             604800   ; Negative Cache TTL
)

@       IN      NS    1dns0.localdomain.
@       IN      A     10.10.10.10

1dns0   IN      A     10.10.10.10
```

`db.10.10`
```bash
$TTL    604800
@       IN      SOA   1dns0.localdomain. root.1dns0.localdomain. (
                  1   ; Serial
             604800   ; Refresh
              86400   ; Retry
            2419200   ; Expire
             604800   ; Negative Cache TTL
)

@       IN      NS    1dns0.localdomain.
@       IN      A     10.10.10.10

10.10   IN      PTR   1dns0.localdomain.
```
