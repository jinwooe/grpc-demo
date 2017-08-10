# Generate keys and certificates

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

# Enable TLS
## gRPC Server
```java
int port = 50051;
server = ServerBuilder.forPort(port)
         .useTransportSecurity(new File("crypto/server.crt"), new File("crypto/key.pem"))
         .addService(new GreeterImpl())
         .build()
         .start();
```
## gPRC Client
```java
NettyChannelBuilder.forAddress(host, port)
                .sslContext(GrpcSslContexts.forClient().trustManager(new File("crypto/server.crt")).build())
                .build();
```

Note that the host which grpc client tries to connect must be the same as  the "common name" of the server's certificate
