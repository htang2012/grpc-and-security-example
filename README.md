The problems that I had with SSL comes from mix secure and insecure client/server (SSL routines:OPENSSL_internal:WRONG_VERSION_NUMBER) and from the bad use of certificates for grpc (SSL routines:OPENSSL_internal:CERTIFICATE_VERIFY_FAILED), certificates that works with other libs (ZeroC Ice for example).

The gen-certs.sh script generates valid certificates for grpc. Be carefull changin the OU and CN from the subject. I recommend not use IP addresses in CN because I got some error doing that, is better use hostnames like localhost. Use or set names to the IP that you want to use in /etc/hosts.

