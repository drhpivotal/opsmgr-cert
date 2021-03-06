# opsmgr-cert-ssl.sh

## Description	 

A short script to strengthen the default security posture of a Pivotal Operations Manager instance. The script provides the following enhancements:

**Acquire [LetsEncrypt](https://letsencrypt.org/) SSL Certificate:**
* Install [certbot](https://certbot.eff.org/) tooling
* Request valid LetEncrypt SSL cert against the OpsManager URL
* Replace OpsManager self-signed cert with LetsEncrypt provided SSL cert
* Update crontab to auto-renew certificate ongoing

**Tighten up NGINX SSL security controls:**
* Restrict negotiable ciphers to elliptical curve diffie-hellman for key exchange and forward secrecy (FS/PFS), AES128-GCM/AES256-GCM cipher for encryption  *ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384*
* Restrict allowable SSL protocols to TLS 1.2 only
* Enable ssl_prefer_server_ciphers
* Enable ssl_stapling enabled with verification
* Set ssl_session_cache set to 10 mins
* Set ssl_session timeout to 24hrs
* Generate strengthened Diffie-Hellman prime to 2048

Implementing the above controls should produce an A+ rating using the [Qualys SSL Server Test](https://www.ssllabs.com/ssltest/).

## Requirements
From the Ops Manager VM:
- outbound Internet access against *.api.letsencrypt.org to pull relevant certbot (LetsEncrypt) tooling for cert instantiation. 
- Internet inbound HTTPS (TCP443) from 0.0.0.0/0 for cert verification

Tested against OpsManager releases 1.9.x, 1.10.x, 1.11.x

## Installation
The script must be run from within the OpsManager VM.
```
git clone https://github.com/drhpivotal/opsmgr-cert-ssl.git
cp opsmgr-cert-ssl/opsmgr-cert-ssl.sh /tmp
chmod +x /tmp/opsmgr-cert-ssl.sh
/tmp/opsmgr-cert-ssl.sh {opsmgr FQDN} {email}
```

## A Word on OpsManager Security
While this script provides additional security measures, it does not preclude the need for applying appropriate WAF and additional perimeter security measures (WAF, firewall, DDoS prevention etc.) The general guidance when it comes to OpsManager security suggests it not be Internet facing and accessible only via Jumpbox under a controlled sub-network (eg. management).
