The problems that I had with SSL comes from mix secure and insecure client/server (SSL routines:OPENSSL_internal:WRONG_VERSION_NUMBER) and from the bad use of certificates for grpc (SSL routines:OPENSSL_internal:CERTIFICATE_VERIFY_FAILED), certificates that works with other libs (ZeroC Ice for example).

The gen-certs.sh script generates valid certificates for grpc. Be carefull changin the OU and CN from the subject. I recommend not use IP addresses in CN because I got some error doing that, is better use hostnames like localhost. Use or set names to the IP that you want to use in /etc/hosts.
 

How to generate self signed SSL certificates:

How to generate self-signed SSL certificates
Jump to navigationJump to search
(Originally copied from [1] by Pascal Scheffers.)



Create a key file
   $ openssl genrsa -out key.pem 1024
To create a certificate request with that key, you will need to answer some questions. The most important one is "Common Name" which MUST be the hostname of the server you're creating a certificate for. Only "Common Name" is required, for the SSL-RFC, but your Certificate Authority (Verisign, Thawte, etc.) will require some of the other fields. Just fill them all out, to be safe. The last two questions may be required for the registration procedure of some certificate authorities.

   $ openssl req -new -key key.pem -out request.pem
   
   Country Name (2 letter code) [AU]:NL
   State or Province Name (full name) [Some-State]:
   Locality Name (eg, city) []:Zeist
   Organization Name (eg, company) [Internet Widgits Pty Ltd]:Pascal's OpenACS Project
   Organizational Unit Name (eg, section) []:.
   Common Name (eg, YOUR name) []:pascal.scheffers.net
   Email Address []:pascal@scheffers.net
   
   Please enter the following 'extra' attributes
   to be sent with your certificate request
   A challenge password []:
   An optional company name []:
You can now send the request.pem file to some commercial Certificate Authority and wait for the certificate, or create your own "self-signed" certificate for testing purposes.

To create the self-signed certificate, do the following:

   $ openssl req -x509 -days 30 -key key.pem -in request.pem -out certificate.pem
Update: This may be wrong. The right way may be:

   $ openssl x509 -req -days 30 -in request.pem -signkey key.pem -out certificate.pem
Now you have a self-signed certificate.

When you get your actual certificate from the CA, you may have to convert it from binary/DER to PEM format:

   $ openssl x509 -inform der -in certificate.crt -out certificate.pem
Obtaining a server certificate with Internet Explorer
Most Certificate Authorities have an website that lets you request a certificate with Internet Explorer. It is very simple to convert such a certificate to a cert.pem and key.pem file. After you have obtained the certificate (do make sure that you keep the key exportable when you request the certificate!), you must first export it to a file:

From internet explorer, open the Tools menu, and choose Internet options. On the 'Content' tab click the 'Certificates' button. Select your server certificate and click 'export'.

Select that you want to export the private key, on the .PFX options, DO NOT include all certificate in the path and DO NOT enable strong protection. When asked to, provide a nice and long password if you need to transfer the resulting certificate.pfx file over email.

Now on your server machine:

   $ cd /home/aolserver/servers/server1/modules/nsopenssl
   $ openssl pkcs12 -nodes -in certificate.pfx -out certificate.pem 
The resulting certificate.pem will contain both the certificate AND the key. You can either remove the key from this file and put it in a key.pem file or do as I do: change the keyfile parameter in your config.tcl from ".../key.pem" to ".../certificate.pem", so that the server looks in certificate.pem for both the key AND certificate.

Credits
This document was written by Pascal Scheffers (pascal@scheffers.net) for the OpenACS project. I relied on the documentation efforts of the people from the OpenSSL project and the nsopenssl readme.txt. Thank you.

The subset of this document on the wiki was edited by Dossy Shiobara.


