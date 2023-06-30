### my-xmpp-docker-compose
Tigase XMPP Server can have a full-fledged XMPP server in Docker in a matter of minutes.

# What is Tigase XMPP server?

[Tigase XMPP Server](https://github.com/tigase/tigase-server/) is scalable and performant server implementation of the XMPP protocol written in Java.

For more information about Tigase XMPP Server and related products, please visit https://tigase.net.

Documentation for Tigase XMPP Server is available at https://docs.tigase.net.

Available from [DockerHub](https://hub.docker.com/r/tigase/tigase-xmpp-server)

![Tigase logo](logo.png)

### Automatic Installs
```
https://github.com/WhateverItWorks/Watchtower
```

### Deploy with Docker

```
docker-compose up -d
```

### Deploy with Docker-Compose

```
docker-compose down
docker-compose pull
docker-compose up -d
```

### Deploy Manually

- [Setup Prosody XMPP Server on Ubuntu 22.04](https://www.linuxbabe.com/ubuntu/prosody-xmpp-server-ubuntu-22-04)
- [Setup Prosody XMPP Server on Ubuntu 22.04](https://www.linuxbabe.com/ubuntu/install-configure-prosody-xmpp-server-ubuntu-20-04)

### View Logs

```
docker logs xmpp
```

## Security Audits:

- [Internet.nl](https://internet.nl/site/element.whateveritworks.org/2060148/)
- [HSTS Preload](https://hstspreload.org/)
- [SSL Labs](https://www.ssllabs.com/ssltest/analyze.html?d=element.whateveritworks.org)
- [Security Headers](https://securityheaders.com/?q=element.whateveritworks.org&hide=on&followRedirects=on)
- [pagespeed](https://pagespeed.web.dev/)
- [webbkoll](https://webbkoll.dataskydd.net/en)
- [ImmuniWeb](https://www.immuniweb.com/ssl/element.whateveritworks.org)
- [Hardenize](https://www.hardenize.com/report/element.whateveritworks.org/1686343966)
- [Mozilla.org](https://observatory.mozilla.org/)
- [report-uri.com](https://report-uri.com/home/tools)
- [check-your-website.server-daten.de](https://check-your-website.server-daten.de/?q=element.whateveritworks.org)
- [csp-evaluator.withgoogle.com](https://csp-evaluator.withgoogle.com/)
- [OpenWPM](https://github.com/openwpm/OpenWPM)
- [privacyscore.org](https://privacyscore.org)

## Usage:

1. Buy [Hetzner.com](https://hetzner.com) it's 100% renewal hardware and you get affordable dedicated servers, and you also help save the world.

2. Get [Cloudflare](https://cloudflare.com) it's carbon renewal and you help save the world.

3. ```apt install git```

4. ```git clone https://github.com/WhateverItWorks/my-xmpp-docker-compose.git xmpp```

5. ```nano docker-compose.yml```

6. ```docker-compose up -d```

```http://localhost:8080```

### XMPP Server Hardening

- [Securing XMPP](https://wiki.xmpp.org/web/Securing_XMPP)

### XMPP Server Testing

- [Compliance Checker](https://compliance.conversations.im/)
- [Connection Checker](https://connect.xmpp.net/)
- [Observatory.mozilla.org](https://observatory.mozilla.org/)
- [CryptCheck](https://cryptcheck.fr/)
- [tls.imirhil.fr](https://tls.imirhil.fr/)

### XMPP Cross Platform

- [Download a XMPP Client](https://xmpp.org/getting-started/)


## Configuration

### Exposing ports

Tigase XMPP Server as the XMPP server is only useful if accessible from the outside of the container. Tigase exposes following ports:
```
- `5222` - for incoming client to server XMPP connections (over StartTLS)
- `5223` - for incoming client to server XMPP connections (over DirectTLS/SSL)
- `5269` - for federated XMPP connections (s2s)
- `5277` - for inter-cluster communication
- `5280` - for BOSH connections
- `5281` - for BOSH connections over TLS/SSL
- `5290` - for WebSocket connections
- `5291` - for WebSocket connections over TLS/SSL
- `8080` - for HTTP server (web-based setup, REST API, file upload extension, etc.)
- `9050` - for JXM monitoring
```
Docker image defines all of the above ports as exportable, however it depends on the Tigase XMPP Server configuration if particular service is available at any of those ports.


### Operating system settings

When running Tigase XMPP Server in production it's essential to apply configuration outlined in [Linux Settings for High Load Systems](https://docs.tigase.net/tigase-server/master-snapshot/Administration_Guide/html/#linuxhighload). In case of Tigase's Docker image this is done via parameters applied to `docker run`:
* [`--sysctl map` (Sysctl options (default map[]))](https://docs.docker.com/engine/reference/commandline/run/#configure-namespaced-kernel-parameters-sysctls-at-runtime)
* [`--ulimit ulimit` (Ulimit options (default []))](https://docs.docker.com/engine/reference/commandline/run/#set-ulimits-in-container---ulimit)


#### TCP network settings

Network configuration adjustments are mostly needed if online user status is not detected correctly after user is disconnected (or with a significant delay).

```
--sysctl "net.ipv4.tcp_keepalive_time=60" \
--sysctl "net.ipv4.tcp_keepalive_probes=3" \
--sysctl "net.ipv4.tcp_keepalive_intvl=90" \
--sysctl "net.ipv4.ip_local_port_range=1024 65000" 
```
