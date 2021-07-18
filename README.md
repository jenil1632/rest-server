# rest-server
REST Server application to test mutual SSL

#CREATE A NEW CERTIFICATE AUTHORITY
keytool -v -genkeypair -dname "CN=Root-CA,OU=Certificate Authority,O=Thunderberry,C=NL" -keystore C:/Users/jenilshah/identity.jks -storepass secret -keypass secret -keyalg RSA -keysize 2048 -alias root-ca -validity 3650 -deststoretype pkcs12 -ext KeyUsage=digitalSignature,keyCertSign -ext BasicConstraints=ca:true,PathLen:3

#CREATE A NEW SERVER KEYSTORE WITH PUBLIC/PRIVATE KEY PAIR
keytool -v -genkeypair -dname "CN=localhost,OU=Amsterdam,O=Thunderberry,C=NL" -keystore C:/Users/jenilshah/server-keystore.jks -storepass secret -keypass secret -keyalg RSA -keysize 2048 -alias server -validity 3650 -deststoretype pkcs12

#CREATE A CERTIFICATE SIGNING REQUEST FOR SERVER
keytool -v -certreq -file C:/Users/jenilshah/server.csr -keystore C:/Users/jenilshah/server-keystore.jks -alias server -keypass secret -storepass secret -keyalg rsa

#CREATE A NEW CLIENT KEYSTORE WITH PUBLIC/PRIVATE KEY PAIR
keytool -v -genkeypair -dname "CN=localhost,OU=Altindag,O=Altindag,C=NL" -keystore C:/Users/jenilshah/client-keystore.jks -storepass secret -keypass secret -keyalg RSA -keysize 2048 -alias client -validity 3650 -deststoretype pkcs12

#CREATE A CERTIFICATE SIGNING REQUEST FOR CLIENT 
keytool -v -certreq -file C:/Users/jenilshah/client.csr -keystore C:/Users/jenilshah/client-keystore.jks -alias client -keypass secret -storepass secret -keyalg rsa

#CREATE A SIGNED SERVER CERTIFICATE 
keytool -v -gencert -infile C:/Users/jenilshah/client.csr -outfile C:/Users/jenilshah/client-signed.cer -keystore C:/Users/jenilshah/identity.jks -storepass secret -alias root-ca -validity 3650 -ext KeyUsage=digitalSignature,dataEncipherment,keyEncipherment,keyAgreement -ext ExtendedKeyUsage=serverAuth,clientAuth -rfc

#CREATE A SIGNED CLIENT CERTIFICATE
keytool -v -gencert -infile C:/Users/jenilshah/server.csr -outfile C:/Users/jenilshah/server-signed.cer -keystore C:/Users/jenilshah/identity.jks -storepass secret -alias root-ca -validity 3650 -ext KeyUsage=digitalSignature,dataEncipherment,keyEncipherment,keyAgreement -ext ExtendedKeyUsage=serverAuth,clientAuth -rfc

#EXPORT THE CERTIFICATE AUTHORITY PUBLIC/PRIVATE KEY PAIR
keytool -v -exportcert -file C:/Users/jenilshah/root-ca.pem -alias root-ca -keystore C:/Users/jenilshah/identity.jks -storepass secret -rfc


#IMPORT THE CA KEY PAIR FIRST INTO SERVER KEYSTORE
keytool -v -importcert -file C:/Users/jenilshah/root-ca.pem -alias root-ca -keystore C:/Users/jenilshah/server-keystore.jks -storepass secret -noprompt
#IMPORT THE SIGNED SERVER CERTIFICATE INTO SERVER KEYSTORE
keytool -v -importcert -file C:/Users/jenilshah/server-signed.cer -alias server -keystore C:/Users/jenilshah/server-keystore.jks -storepass secret
#DELETE THE CA KEY PAIR FROM SERVER KEYSTORE
keytool -v -delete -alias root-ca -keystore C:/Users/jenilshah/server-keystore.jks -storepass secret



#IMPORT THE CA PUBLIC CERTIFICATE INTO CLIENT TRUSTSTORE
keytool -v -importcert -file C:/Users/jenilshah/root-ca.pem -alias root-ca -keystore C:/Users/jenilshah/client-truststore.jks -storepass secret -noprompt

#IMPORT THE CA PUBLIC CERTIFICATE INTO SERVER TRUSTSTORE
keytool -v -importcert -file C:/Users/jenilshah/root-ca.pem -alias root-ca -keystore C:/Users/jenilshah/server-truststore.jks -storepass secret -noprompt


#wrong keystore TO SIMULATE EMPTY CERTIFICATE CHAIN
keytool -v -genkeypair -dname "CN=Suleyman,OU=Altindag,O=Altindag,C=NL" -keystore C:/Users/jenilshah/client-keystore.jks -storepass secret -keypass secret -keyalg RSA -keysize 2048 -alias client -validity 3650 -deststoretype pkcs12 


#IMPORT CA KEY PAIR FISRT INTO CLIENT TRUSTSTORE
keytool -v -importcert -file C:/Users/jenilshah/root-ca.pem -alias root-ca -keystore C:/Users/jenilshah/client-keystore.jks -storepass secret -noprompt
#IMPORT SIGNED CLIENT CERTIFICATE INTO CLIENT KEYSTORE
keytool -v -importcert -file C:/Users/jenilshah/client-signed.cer -alias client -keystore C:/Users/jenilshah/client-keystore.jks -storepass secret
#DELETE THE CA KEY PAIR FROM CLIENT TRUSTSTORE
keytool -v -delete -alias root-ca -keystore C:/Users/jenilshah/client-keystore.jks -storepass secret



##IMPORTANT###

RUN APPLICATION WITH THESE VM ARGUMENTS

-Djavax.net.debug=all -Djavax.net.ssl.trustStore=C:\Users\jenilshah\eclipse-workspace\rest-server\src\main\resources\server-truststore.jks -Djavax.net.ssl.trustStorePassword=secret









