# Домашняя работа: IPSec over DmVPN

### Цель: Настроить GRE поверх IPSec между офисами Москва и С.-Петербург


- #### Настроить GRE поверх IPSec между офисами Москва и С.-Петербург

- #### Настроить DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги

- #### Для IPSec использовать CA и сертификаты



### Настроить GRE поверх IPSec между офисами Москва и С.-Петербург

**Настроим CA на R15:**

```
R15(config)#ip domain-name msk.local
R15(config)#ip http server
R15(config)#$generate rsa general-keys label CA exportable modulus 4096
The name for the keys will be: CA

% The key modulus size is 4096 bits
% Generating 4096 bit RSA keys, keys will be exportable...
[OK] (elapsed time was 10 seconds)
R15(config)#
Mar 14 17:32:13.955: %SSH-5-ENABLED: SSH 1.99 has been enabled
R15(config)#crypto pki server CA
R15(cs-server)#grant auto
R15(cs-server)#no shut
%Some server settings cannot be changed after CA certificate generation.
% Please enter a passphrase to protect the private key
% or type Return to exit
Password:
Re-enter password:
% Certificate Server enabled.
Mar 14 17:32:45.091: %PKI-6-CS_ENABLED: Certificate server now enabled.
```

**Настроим сертификат на R18:**

```
R18(config)#crypto key generate rsa label VPN modulus 4096
The name for the keys will be: VPN

% The key modulus size is 4096 bits
% Generating 4096 bit RSA keys, keys will be non-exportable...
[OK] (elapsed time was 16 seconds)

R18(config)#
*Mar 14 17:35:25.144: %SSH-5-ENABLED: SSH 1.99 has been enabled
R18(config)#crypto pki trustpoint VPN
R18(ca-trustpoint)#enrollment url http://10.255.100.15
R18(ca-trustpoint)#subject-name CN=R18,OU=VPN,O=MSK,C=RU
R18(ca-trustpoint)#rsakeypair VPN
R18(ca-trustpoint)#revocation-check none
R18(ca-trustpoint)#crypto pki authenticate VPN
Certificate has the following attributes:
       Fingerprint MD5: AF54D3A6 8BD3D2D3 48AA48E1 6D19CDAE
      Fingerprint SHA1: D7E97CE5 6401D283 264F6A67 52AA1EC3 AFBF0064

% Do you accept this certificate? [yes/no]: yes
Trustpoint CA certificate accepted.
R18(ca-trustpoint)#crypto pki enroll VPN
%
% Start certificate enrollment ..
% Create a challenge password. You will need to verbally provide this
   password to the CA Administrator in order to revoke your certificate.
   For security reasons your password will not be saved in the configuration.
   Please make a note of it.
Password:
Re-enter password:
% The subject name in the certificate will include: CN=R18,OU=VPN,O=MSK,C=RU
% The subject name in the certificate will include: R18
% Include the router serial number in the subject name? [yes/no]: no
% Include an IP address in the subject name? [no]: no
Request certificate from CA? [yes/no]: yes
% Certificate request sent to Certificate Authority
% The 'show crypto pki certificate verbose VPN' commandwill show the fingerprint.
*Mar 14 17:36:46.267: CRYPTO_PKI:  Certificate Request Fingerprint MD5: CE89069D 0DA42010 4710234F F6443DEF
*Mar 14 17:36:46.267: CRYPTO_PKI:  Certificate Request Fingerprint SHA1: E172B6D6 61E1BF73 D935B3E0 B5F2A01E 5EE533FB
*Mar 14 17:36:47.586: %PKI-6-CERTRET: Certificate received from Certificate Authority
```

**Настроим сертификат на R15:**

```
R15(config)#crypto pki trustpoint I_CA
R15(ca-trustpoint)#enrollment url http://10.255.100.15
R15(ca-trustpoint)#subject-name CN=R15,OU=VPN,O=MSK,C=RU
R15(ca-trustpoint)#revocation-check none
R15(ca-trustpoint)#rsakeypair I_CA
R15(ca-trustpoint)#crypto pki authenticate I_CA
Certificate has the following attributes:
       Fingerprint MD5: AF54D3A6 8BD3D2D3 48AA48E1 6D19CDAE
      Fingerprint SHA1: D7E97CE5 6401D283 264F6A67 52AA1EC3 AFBF0064

% Do you accept this certificate? [yes/no]: yes
Trustpoint CA certificate accepted.
R15(config)#crypto pki enroll I_CA
%
% Start certificate enrollment ..
% Create a challenge password. You will need to verbally provide this
   password to the CA Administrator in order to revoke your certificate.
   For security reasons your password will not be saved in the configuration.
   Please make a note of it.
Password:
Mar 14 17:38:56.383: %CRYPTO-6-AUTOGEN: Generated new 512 bit key pair
Re-enter password:
% The subject name in the certificate will include: CN=R15,OU=VPN,O=MSK,C=RU
% The subject name in the certificate will include: R15.msk.local
% Include the router serial number in the subject name? [yes/no]: no
% Include an IP address in the subject name? [no]: no
Request certificate from CA? [yes/no]: yes
% Certificate request sent to Certificate Authority
% The 'show crypto pki certificate verbose I_CA' commandwill show the fingerprint.
Mar 14 17:39:11.888: CRYPTO_PKI:  Certificate Request Fingerprint MD5: 56CE1B16 375F66E3 AD559C75 48955B07
Mar 14 17:39:11.888: CRYPTO_PKI:  Certificate Request Fingerprint SHA1: D9C260C9 F82F97C7 A83E7B6F BBC85A65 4BABCCDE
Mar 14 17:39:12.518: %PKI-6-CERTRET: Certificate received from Certificate Authority
```

**Настроим IPSec на R15 и R18**:

```
crypto isakmp policy 10
authentication rsa-sig 
crypto ipsec transform-set SET esp-aes esp-sha-hmac
crypto ipsec profile VTI_prof
set transform-set SET
interface Tunnel 100
tunnel protection ipsec profile VTI_prof
```

**Проверим:**

```
R18#show crypto session
*Mar 14 17:42:26.316: %SYS-5-CONFIG_I: Configured from console by console
R18#show crypto session
Crypto session current status

Interface: Tunnel100
Session status: UP-ACTIVE
Peer: 108.42.2.154 port 500
  IKEv1 SA: local 142.17.201.170/500 remote 108.42.2.154/500 Active
  IKEv1 SA: local 142.17.201.170/500 remote 108.42.2.154/500 Active
  IPSEC FLOW: permit 47 host 142.17.201.170 host 108.42.2.154
        Active SAs: 4, origin: crypto map
```

```
R18#sh crypto isakmp peers
Peer: 108.42.2.154 Port: 500 Local: 142.17.201.170
 Phase1 id: R15.msk.local
```



### Настроим DMVPN поверх IPSec между Москва и Чокурдах, Лабытнанги

**Настроим CA на R14:**

```
R14(config)#ip domain-name msk.local
R14(config)#ip http server
R14(config)#$generate rsa general-keys label CA exportable modulus 4096
The name for the keys will be: CA
% The key modulus size is 4096 bits
% Generating 4096 bit RSA keys, keys will be exportable...
[OK] (elapsed time was 15 seconds)
R14(config)#crypto pki server CA
R14(cs-server)#grant auto
R14(cs-server)#no shut
%Some server settings cannot be changed after CA certificate generation.
% Please enter a passphrase to protect the private key
% or type Return to exit
Password:
Re-enter password:
% Certificate Server enabled.
```

**Настроим сертификат на R28:**

```
R28(config)#crypto key generate rsa label VPN modulus 4096
The name for the keys will be: VPN

% The key modulus size is 4096 bits
% Generating 4096 bit RSA keys, keys will be non-exportable...
[OK] (elapsed time was 16 seconds)

R28(config)#
*Mar 14 17:16:33.941: %SSH-5-ENABLED: SSH 1.99 has been enabled
R28(config)#crypto pki trustpoint VPN
R28(ca-trustpoint)#enrollment url http://10.200.200.14
R28(ca-trustpoint)#subject-name CN=R28,OU=VPN,O=MSK,C=RU
R28(ca-trustpoint)#rsakeypair VPN
R28(ca-trustpoint)#revocation-check none
R28(ca-trustpoint)#crypto pki authenticate VPN
Certificate has the following attributes:
       Fingerprint MD5: 059CB6F9 0C03DE36 93FB5460 B3190E03
      Fingerprint SHA1: DCF87B3B D0ED59AF E8FC091C E2690DA6 DCCE9E40

% Do you accept this certificate? [yes/no]: yes
Trustpoint CA certificate accepted.
R28(config)#crypto pki enroll VPN
%
% Start certificate enrollment ..
% Create a challenge password. You will need to verbally provide this
   password to the CA Administrator in order to revoke your certificate.
   For security reasons your password will not be saved in the configuration.
   Please make a note of it.
Password:
Re-enter password:
% The subject name in the certificate will include: CN=R28,OU=VPN,O=MSK,C=RU
% The subject name in the certificate will include: R28
% Include the router serial number in the subject name? [yes/no]: no
% Include an IP address in the subject name? [no]: no
Request certificate from CA? [yes/no]: yes
% Certificate request sent to Certificate Authority
% The 'show crypto pki certificate verbose VPN' commandwill show the fingerprint.
*Mar 14 17:17:44.614: CRYPTO_PKI:  Certificate Request Fingerprint MD5: E1EAD570 D3250D33 2A26B32B F52CACAD
*Mar 14 17:17:44.614: CRYPTO_PKI:  Certificate Request Fingerprint SHA1: CCF85DEE 6537816E 1DA7070F 48AE7F8B FC275A02
R28(config)#
*Mar 14 17:17:45.938: %PKI-6-CERTRET: Certificate received from Certificate Authority
```

**Настроим сертификат на R27:**

```
R27(config)#crypto key generate rsa label VPN modulus 4096
The name for the keys will be: VPN
% The key modulus size is 4096 bits
% Generating 4096 bit RSA keys, keys will be non-exportable...
[OK] (elapsed time was 14 seconds)
R27(config)#crypto pki trustpoint VPN
R27(ca-trustpoint)#enrollment url http://10.200.200.14
R27(ca-trustpoint)#subject-name CN=R27,OU=VPN,O=MSK,C=RU
R27(ca-trustpoint)#rsakeypair VPN
R27(ca-trustpoint)#revocation-check none
R27(ca-trustpoint)#crypto pki authenticate VPN
Certificate has the following attributes:
       Fingerprint MD5: 059CB6F9 0C03DE36 93FB5460 B3190E03
      Fingerprint SHA1: DCF87B3B D0ED59AF E8FC091C E2690DA6 DCCE9E40

% Do you accept this certificate? [yes/no]: crypto pki enroll VPN
*Mar 14 17:14:22.011: %SSH-5-ENABLED: SSH 1.99 has been enabled
crypto pki enroll VPN
% Please answer 'yes' or 'no'.
% Do you accept this certificate? [yes/no]: yes
Trustpoint CA certificate accepted.
R27(config)#crypto pki enroll VPN
%
% Start certificate enrollment ..
% Create a challenge password. You will need to verbally provide this
   password to the CA Administrator in order to revoke your certificate.
   For security reasons your password will not be saved in the configuration.
   Please make a note of it.
Password:
Re-enter password:
% The subject name in the certificate will include: CN=R27,OU=VPN,O=MSK,C=RU
% The subject name in the certificate will include: R27
% Include the router serial number in the subject name? [yes/no]: no
% Include an IP address in the subject name? [no]: no
Request certificate from CA? [yes/no]: yes
% Certificate request sent to Certificate Authority
% The 'show crypto pki certificate verbose VPN' commandwill show the fingerprint.
*Mar 14 17:18:24.226: CRYPTO_PKI:  Certificate Request Fingerprint MD5: 8A748B06 34A9F12E 22DB63DD 0DFE5B2D
*Mar 14 17:18:24.226: CRYPTO_PKI:  Certificate Request Fingerprint SHA1: 836C913C 730E3C7E D0A07CC5 F61229A7 15BD201E
*Mar 14 17:18:25.483: %PKI-6-CERTRET: Certificate received from Certificate Authority
```

**Настроим сертификат на R14:**

```
R14(config)#crypto pki trustpoint I_CA
R14(ca-trustpoint)#enrollment url http://10.200.200.14
R14(ca-trustpoint)#subject-name CN=R14,OU=VPN,O=MSK,C=RU
R14(ca-trustpoint)#revocation-check none
R14(ca-trustpoint)#rsakeypair I_CA
R14(ca-trustpoint)#crypto pki authenticate I_CA
Certificate has the following attributes:
       Fingerprint MD5: 059CB6F9 0C03DE36 93FB5460 B3190E03
      Fingerprint SHA1: DCF87B3B D0ED59AF E8FC091C E2690DA6 DCCE9E40

% Do you accept this certificate? [yes/no]: yes
Trustpoint CA certificate accepted.
R14(config)#crypto pki enroll I_CA
%
% Start certificate enrollment ..
% Create a challenge password. You will need to verbally provide this
   password to the CA Administrator in order to revoke your certificate.
   For security reasons your password will not be saved in the configuration.
   Please make a note of it.
Password:
Mar 14 17:22:50.105: %CRYPTO-6-AUTOGEN: Generated new 512 bit key pair
Re-enter password:
% The subject name in the certificate will include: CN=R14,OU=VPN,O=MSK,C=RU
% The subject name in the certificate will include: R14.msk.local
% Include the router serial number in the subject name? [yes/no]: no
% Include an IP address in the subject name? [no]: no
Request certificate from CA? [yes/no]: yes
% Certificate request sent to Certificate Authority
% The 'show crypto pki certificate verbose I_CA' commandwill show the fingerprint.
Mar 14 17:23:06.936: CRYPTO_PKI:  Certificate Request Fingerprint MD5: EC92A591 0107E70E BA35870C F771011E
Mar 14 17:23:06.936: CRYPTO_PKI:  Certificate Request Fingerprint SHA1: E2716F9A 97707433 AAA19F8B DFD21011 6C784554
Mar 14 17:23:07.601: %PKI-6-CERTRET: Certificate received from Certificate Authority
```

**Настроим IPSec на R14, R27 и R28:**

```
crypto isakmp policy 10
authentication rsa-sig 
crypto ipsec transform-set SET esp-aes esp-sha-hmac
crypto ipsec profile VTI_prof
set transform-set SET
interface Tunnel 200
tunnel protection ipsec profile VTI_prof
```

**Проверим:**

```
R14#show crypto session
Crypto session current status

Interface: Tunnel200
Session status: UP-ACTIVE
Peer: 142.17.201.78 port 500
  Session ID: 0
  IKEv1 SA: local 108.42.15.214/500 remote 142.17.201.78/500 Active
  Session ID: 0
  IKEv1 SA: local 108.42.15.214/500 remote 142.17.201.78/500 Active
  IPSEC FLOW: permit 47 host 108.42.15.214 host 142.17.201.78
        Active SAs: 2, origin: crypto map

Interface: Tunnel200
Session status: UP-ACTIVE
Peer: 142.17.201.34 port 500
  Session ID: 0
  IKEv1 SA: local 108.42.15.214/500 remote 142.17.201.34/500 Active
  Session ID: 0
  IKEv1 SA: local 108.42.15.214/500 remote 142.17.201.34/500 Active
  IPSEC FLOW: permit 47 host 108.42.15.214 host 142.17.201.34
        Active SAs: 4, origin: crypto map
```

```
R28#sh crypto isakmp peers
Peer: 108.42.15.214 Port: 500 Local: 142.17.201.78
 Phase1 id: R14.msk.local
Peer: 142.17.201.34 Port: 500 Local: 142.17.201.78
 Phase1 id: R27
```

