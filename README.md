# Xiaomi
Block xiaomi tracking you

```
sudo apt-get update
sudo apt-get install squid3
```

## References 

1. https://github.com/hidden-refuge/spi
2. https://www.configserverfirewall.com/ubuntu-linux/install-squid-ubuntu-server/


Edit /etc/squid/blacklist.acl ( Feel free to add more into the list )

```
.mi.com
.appmifile.com
.xiaomi.com
.xiaomi.net
.miui.com
.xiaomi.eu
.spotxchange.com
.krxd.net
.mxpnl.com
.etahub.com
.mi-img.com
.adtiming.com
```

Edit /etc/squid/squid.conf 

```
http_port 4234 # Change your port
cache deny all
# hierarchy_stoplist cgi-bin ?

# Useful if you want to see traffic
access_log /var/log/squid/access.log
cache_store_log none
cache_log /dev/null

refresh_pattern ^ftp: 1440 20% 10080
refresh_pattern ^gopher: 1440 0% 1440
refresh_pattern -i (/cgi-bin/|\?) 0 0% 0
refresh_pattern . 0 20% 4320

acl localhost src 127.0.0.1/32 ::1
acl to_localhost dst 127.0.0.0/8 0.0.0.0/32 ::1

acl SSL_ports port 1-65535
acl Safe_ports port 1-65535
acl CONNECT method CONNECT
acl siteblacklist dstdomain "/etc/squid/blacklist.acl"

# Explicitly allow for google, youtube, playstore etc to work on Android 
acl access_google dstdomain .google.com .googleusercontent.com .googleapis.com .youtube.com .ytimg.com .googlevideo.com .gvt1.com .ggpht.com

http_access allow manager localhost
http_access deny manager

http_access deny !Safe_ports
http_access deny CONNECT !SSL_ports
http_access allow access_google

# http_access allow Safe_ports
# http_access allow CONNECT SSL_ports

http_access deny siteblacklist
auth_param basic program /usr/lib/squid3/basic_ncsa_auth /etc/squid/passwd

auth_param basic children 5
auth_param basic realm Squid proxy-caching web server
auth_param basic credentialsttl 2 hours
acl password proxy_auth REQUIRED
http_access allow localhost
http_access allow password
http_access deny all

forwarded_for off
request_header_access Allow allow all
request_header_access Authorization allow all
request_header_access WWW-Authenticate allow all
request_header_access Proxy-Authorization allow all
request_header_access Proxy-Authenticate allow all
request_header_access Cache-Control allow all
request_header_access Content-Encoding allow all
request_header_access Content-Length allow all
request_header_access Content-Type allow all
request_header_access Date allow all
request_header_access Expires allow all
request_header_access Host allow all
request_header_access If-Modified-Since allow all
request_header_access Last-Modified allow all
request_header_access Location allow all
request_header_access Pragma allow all
request_header_access Accept allow all
request_header_access Accept-Charset allow all
request_header_access Accept-Encoding allow all
request_header_access Accept-Language allow all
request_header_access Content-Language allow all
request_header_access Mime-Version allow all
request_header_access Retry-After allow all
request_header_access Title allow all
request_header_access Connection allow all
request_header_access Proxy-Connection allow all
request_header_access User-Agent allow all
request_header_access Cookie allow all
request_header_access All deny all
```

Start / Restart when ever you made any changes.

```
sudo systemctl start squid
sudo systemctl restart squid
```
