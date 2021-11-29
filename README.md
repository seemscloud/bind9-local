```bash
mkdir -p {forward,reverse}
```

```bash
rndc-confgen | grep -E "^key \"rndc-key\" {" -A 3 > rndc.key
```

```bash
cat > named.conf << "EndOfMessage"
include "/etc/bind/named.conf.options";
include "/etc/bind/named.conf.default-zones";
include "/etc/bind/named.conf.custom-zones";
include "/etc/bind/named.conf.logging";
EndOfMessage
```

```bash
cat > named.conf.options << "EndOfMessage"
options {
  directory "/var/cache/bind";

  forwarders {
    8.8.8.8;
    8.8.4.4;
    1.1.1.1;
    1.0.0.1;
    9.9.9.9;
  };

  recursion yes;
  querylog yes;
  auth-nxdomain no;
  dnssec-validation auto;

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
EndOfMessage
```

```bash
cat > named.conf.default-zones << "EndOfMessage"
zone "." {
        type hint;
        file "/usr/share/dns/root.hints";
};

zone "localhost" {
        type master;
        file "/etc/bind/forward/db.localhost";
};

zone "0.in-addr.arpa" {
        type master;
        file "/etc/bind/reverse/db.0";
};

zone "127.in-addr.arpa" {
        type master;
        file "/etc/bind/reverse/db.127";
};

zone "255.in-addr.arpa" {
        type master;
        file "/etc/bind/reverse/db.255";
};
EndOfMessage
```

```bash
cat > named.conf.logging << "EndOfMessage"
logging {
  channel bind_log {
    file "/var/log/bind/bind.log" versions 3 size 5m;
    severity info;
    print-category yes;
    print-severity yes;
    print-time yes;
  };

  category default {
    bind_log;
  };

  category update {
    bind_log;
  };

  category update-security {
    bind_log;
  };

  category security {
    bind_log;
  };

  category queries {
    bind_log;
  };

  category lame-servers {
    null;
  };
};
EndOfMessage
```

```bash
cat > named.conf.rndc << "EndOfMessage"
include "/etc/bind/rndc.key";

controls {
  inet 127.0.0.1 port 953 allow {
    localhost;
  }
  
  keys {
    rndc-key;
  };
};
EndOfMessage
```

```bash
cat > named.conf.custom-zones << "EndOfMessage"
zone "seems.local" {
  type master;
  file "/etc/bind/forward/db.seems.local";
};

zone "10.10.in-addr.arpa" {
  type master;
  file "/etc/bind/reverse/db.10.10";
};
EndOfMessage
```

```bash
cat > forward/db.localhost << "EndOfMessage"
$TTL        604800
@                        IN    SOA    localhost. root.localhost. (
        2021112823    ;  Serial
            604800    ;  Refresh
             86400    ;  Retry
           2419200    ;  Expire
            604800    ;  Negative Cache TTL
)

@                        IN    NS     localhost.
@                        IN    A      127.0.0.1
EndOfMessage
```

```bash
cat > forward/db.seems.local << "EndOfMessage"
$TTL        604800
@                        IN    SOA    1dns0.seems.local. root.1dns0.seems.local. (
        2021112823    ;  Serial
            604800    ;  Refresh
             86400    ;  Retry
           2419200    ;  Expire
            604800    ;  Negative Cache TTL
)

@                        IN    NS     1dns0.seems.local.
@                        IN    A      10.10.10.10

1dns0                    IN    A      10.10.10.10
EndOfMessage
```

```bash
cat > reverse/db.0 << "EndOfMessage"
$TTL        604800
@                        IN    SOA    localhost. root.localhost. (
        2021112823    ;  Serial
            604800    ;  Refresh
             86400    ;  Retry
           2419200    ;  Expire
            604800    ;  Negative Cache TTL
)

@                        IN    NS     localhost.
EndOfMessage
```

```bash
cat > reverse/db.127 << "EndOfMessage"
$TTL        604800
@                        IN    SOA    localhost. root.localhost. (
        2021112823    ;  Serial
            604800    ;  Refresh
             86400    ;  Retry
           2419200    ;  Expire
            604800    ;  Negative Cache TTL
)

@                        IN    NS     localhost.
1.0.0                    IN    PTR    localhost.
EndOfMessage
```

```bash
cat > reverse/db.255 << "EndOfMessage"
$TTL        604800
@                        IN    SOA    localhost. root.localhost. (
        2021112823    ;  Serial
            604800    ;  Refresh
             86400    ;  Retry
           2419200    ;  Expire
            604800    ;  Negative Cache TTL
)

@                        IN    NS     localhost.
EndOfMessage
```

```bash
cat > reverse/db.10.10 << "EndOfMessage"
$TTL        604800
@                        IN    SOA    1dns0.seems.local. root.1dns0.seems.local. (
        2021112823    ;  Serial
            604800    ;  Refresh
             86400    ;  Retry
           2419200    ;  Expire
            604800    ;  Negative Cache TTL
)

@                        IN    NS     1dns0.seems.local.
@                        IN    A      10.10.10.10

10.10   IN      PTR   1dns0.seems.local.
EndOfMessage
```
