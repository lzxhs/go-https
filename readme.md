## 双向认证
go 1.15版后仅支持SAN证书，创建本机运行的SAN证书流程如下：

1. 创建CA根证书

   ```shell
   $openssl genrsa -out ca.key 2048 
   $openssl req -sha256 -new -x509 -days 36500 -key ca.key -out ca.crt -subj "/C=CN/ST=ZJ/L=HZ/O=Learn/OU=GH/CN=localhost" 
   ```

2. 创建服务端证书

   ```shell
   $openssl genrsa -out server.key 2048
   $openssl req -new \
       -sha256 \
       -key server.key \
       -subj "/C=CN/ST=ZJ/L=HZ/O=Learn/OU=GH/CN=localhost" \
       -reqexts SAN \
       -config <(cat ./openssl.cnf \
           <(printf "[SAN]\nsubjectAltName=DNS:localhost.test,DNS:localhost")) \
       -out server.csr
   
    $openssl x509 -req -days 36500 \
     -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial \
     -extfile <(printf "subjectAltName=DNS:localhost.test,DNS:localhost") \
     -out server.crt  
   ```

3. 创建客户端证书

   ```shell
   $openssl genrsa -out client.key 2048
   $openssl req -new \
       -sha256 \
       -key client.key \
       -subj "/C=CN/ST=ZJ/L=HZ/O=Learn/OU=GH/CN=localhost" \
       -reqexts SAN \
       -config <(cat ./openssl.cnf \
           <(printf "[SAN]\nsubjectAltName=DNS:localhost.test,DNS:localhost")) \
       -out client.csr
   
     $openssl x509 -req -days 36500 \
     -in client.csr -CA ca.crt -CAkey ca.key -CAcreateserial \
     -extfile <(printf "subjectAltName=DNS:localhost.test,DNS:localhost") \
     -out client.crt
   ```