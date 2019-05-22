# Google Analytics Bypassing Adblockers

## Client

change www.googletagmanager.com => your.domain.com
```
<!-- Global site tag (gtag.js) - Google Analytics -->
<script async src="https://your.domain.com/gtag/js?id=UA-123456789-1"></script>
```

## Nginx File
```
server {
  listen 80;

  server_name your.domain.com;

  access_log off;
  resolver 8.8.8.8 ipv6=off;

  set $ga 'www.google-analytics.com';

  location = /gtag/js {
    proxy_set_header Accept-Encoding "";
    sub_filter $ga $server_name;
    sub_filter_types *;
    sub_filter_once off;

    proxy_pass https://www.googletagmanager.com$uri$is_args$args;
    break;
  }

  location = /analytics.js {
    proxy_set_header Accept-Encoding "";
    sub_filter $ga $server_name;
    sub_filter '"/r/collect' '"/r/c';
    sub_filter '"/j/collect' '"/j/c';
    sub_filter '"/collect' '"/c';
    sub_filter_types *;
    sub_filter_once off;

    proxy_pass https://$ga/analytics.js;
    break;
  }

  location / {
    if ($uri ~ /r/c){
      proxy_pass https://$ga/r/collect$is_args$args&uip=$remote_addr;
      break;
    }
    if ($uri ~ /j/c){
      proxy_pass https://$ga/j/collect$is_args$args&uip=$remote_addr;
      break;
    }
    if ($uri ~ /c){
      proxy_pass https://$ga/collect$is_args$args&uip=$remote_addr;
      break;
    }

    proxy_pass https://$ga$uri$is_args$args&uip=$remote_addr;
  }


  # Cloudflare
  set_real_ip_from 103.21.244.0/22;
  set_real_ip_from 103.22.200.0/22;
  set_real_ip_from 103.31.4.0/22;
  set_real_ip_from 104.16.0.0/12;
  set_real_ip_from 108.162.192.0/18;
  set_real_ip_from 131.0.72.0/22;
  set_real_ip_from 141.101.64.0/18;
  set_real_ip_from 162.158.0.0/15;
  set_real_ip_from 172.64.0.0/13;
  set_real_ip_from 173.245.48.0/20;
  set_real_ip_from 188.114.96.0/20;
  set_real_ip_from 190.93.240.0/20;
  set_real_ip_from 197.234.240.0/22;
  set_real_ip_from 198.41.128.0/17;
  set_real_ip_from 2400:cb00::/32;
  set_real_ip_from 2606:4700::/32;
  set_real_ip_from 2803:f800::/32;
  set_real_ip_from 2405:b500::/32;
  set_real_ip_from 2405:8100::/32;
  set_real_ip_from 2c0f:f248::/32;
  set_real_ip_from 2a06:98c0::/29;

  # use any of the following two
  real_ip_header CF-Connecting-IP;
}
```
