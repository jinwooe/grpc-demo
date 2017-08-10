# grpc-demo

```sh
# Generate CA key
openssl genrsa -des3 -out ca.key 2048

# Generate CA certificate
openssl req -new -x509 -days 365 -key ca.key -out ca.crt

# Generate server key
openssl genrsa -des3 -out server.key 2048

# Generate server signing request
openssl req -new -key server.key -out server.csr

# Self-sign server certificate
openssl x509 -req -days 365 -in server.csr -CA ca.crt -CAkey ca.key -set_serial 01 -out server.crt

# Remove passphrase from server key
openssl rsa -in server.key -out server.key

# Convert the server private key to a format understood by Java 
openssl pkcs8 -topk8 -inform PEM -outform PEM -in server.key -out key.pem -nocrypt

```
