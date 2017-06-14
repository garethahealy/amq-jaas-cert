# Generate Certificates for AMQ
**Pre-req: Set env passwords**
CLIENT_KEYSTORE_PASSWORD=password
CLIENT_TRUSTSTORE_PASSWORD=password
BROKER_KEYSTORE_PASSWORD=password
BROKER_TRUSTSTORE_PASSWORD=password

**1. Create Client KeyStore**
$JAVA_HOME/bin/keytool \
 -genkey \
 -keyalg RSA \
 -alias amq-client \
 -keystore amq-client.ks \
 -storepass $CLIENT_KEYSTORE_PASSWORD \
 -keypass $CLIENT_KEYSTORE_PASSWORD \
 -dname "CN=client-amqtest-amq-tcp-ssl, O=GarethHealy, C=UK"

**2. Create Server KeyStore**
$JAVA_HOME/bin/keytool \
 -genkey \
 -keyalg RSA \
 -alias amq-broker \
 -keystore amq-broker.ks \
 -storepass $BROKER_KEYSTORE_PASSWORD \
 -keypass $BROKER_KEYSTORE_PASSWORD \
 -dname "CN=broker-amqtest-amq-tcp-ssl, O=GarethHealy, C=UK"

**3. Export Client PublicKey**
$JAVA_HOME/bin/keytool \
 -export \
 -alias amq-client \
 -keystore amq-client.ks \
 -storepass $CLIENT_KEYSTORE_PASSWORD \
 -file amq-client.cert

**4. Export Server PublicKey**
$JAVA_HOME/bin/keytool \
  -export \
  -alias amq-broker \
  -keystore amq-broker.ks \
  -storepass $BROKER_KEYSTORE_PASSWORD \
  -file amq-broker.cert

**5. Import Server PublicKey into Client Truststore**
$JAVA_HOME/bin/keytool \
 -import \
 -alias amq-broker \
 -keystore amq-client.ts \
 -file amq-broker.cert \
 -storepass $CLIENT_TRUSTSTORE_PASSWORD \
 -trustcacerts \
 -noprompt
 
**6. Import Client PublicKey into Server Truststore**
$JAVA_HOME/bin/keytool \
 -import \
 -alias amq-client \
 -keystore amq-broker.ts \
 -file amq-client.cert \
 -storepass $BROKER_TRUSTSTORE_PASSWORD \
 -trustcacerts \
 -noprompt
 
**7. Import Server PublicKey into Server Truststore (i.e.: trusts its self)**
$JAVA_HOME/bin/keytool \
  -import \
  -alias amq-broker \
  -keystore amq-broker.ts \
  -file amq-broker.cert \
  -storepass $BROKER_TRUSTSTORE_PASSWORD \
  -trustcacerts \
  -noprompt
