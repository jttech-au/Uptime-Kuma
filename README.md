# Uptime-Kuma

### Check certificate used on the site
openssl s_client -connect some.domain.com:443 -CApath /etc/ssl/certs

### download cert from site (if required)
openssl s_client -showcerts -connect some.domain.com:443 < /dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > CA1.crt

### Convert cer to crt (if required)
openssl x509 -in CA1.cer -out CA1.crt \
head -n 1 /usr/local/share/ca-certificates/CA1.crt \
You should see. \
-----BEGIN CERTIFICATE-----

### Copy signed certificate files and update
cp CA1.crt /usr/local/share/ca-certificates/ \
sudo chmod 644 /usr/local/share/ca-certificates/CA1.crt \
update-ca-certificates --fresh

### uptime kuma can only have one CA (if required)
cat CA1.crt CA2.crt CA3.crt > CA01.crt

Add the following to compose.yml
```
    volumes:
      - /usr/local/share/ca-certificates:/app/data/docker-tls # self-signed SSL
    environment:
      NODE_EXTRA_CA_CERTS: /app/data/docker-tls/CA01.crt # change certificate name
```
