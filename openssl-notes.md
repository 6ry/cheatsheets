
<http://www.akadia.com/services/ssh_test_certificate.html>
<https://wiki.archlinux.org/index.php/OpenSSL>
OpenSSL Cookbook by Ivan Ristic (Feisty Duck)

<https://www.madboa.com/geek/openssl/>

###See All Available Commands
```
$ openssl help

openssl:Error: 'help' is an invalid command.

Standard commands
asn1parse         ca                ciphers           cms
crl               crl2pkcs7         dgst              dh
dhparam           dsa               dsaparam          ec
ecparam           enc               engine            errstr
gendh             gendsa            genpkey           genrsa
nseq              ocsp              passwd            pkcs12
pkcs7             pkcs8             pkey              pkeyparam
pkeyutl           prime             rand              req
rsa               rsautl            s_client          s_server
s_time            sess_id           smime             speed
spkac             srp               ts                verify
version           x509
```

The ﬁrst part of the help output lists all available utilities. To get more
information about acular utility, use the man command followed by the name of
the utility. For example, manhers will give you detailed information on how
cipher suites are conﬁgured.

Help output doesn’t actually end there, but the rest is somewhat less
interesting. In the secondyou get the list of message digest commands:

    Message Digest commands (see the `dgst' command for more details)
    md4               md5               rmd160            sha
    sha1

And then, in the third part, you’ll see the list of all cipher commands:

    Cipher commands (see the `enc' command for more details)
    aes-128-cbc       aes-128-ecb       aes-192-cbc       aes-192-ecb
    aes-256-cbc       aes-256-ecb       base64            bf
    bf-cbc            bf-cfb            bf-ecb            bf-ofb
    camellia-128-cbc  camellia-128-ecb  camellia-192-cbc  camellia-192-ecb
    camellia-256-cbc  camellia-256-ecb  cast              cast-cbc
    cast5-cbc         cast5-cfb         cast5-ecb         cast5-ofb
    des               des-cbc           des-cfb           des-ecb
    des-ede           des-ede-cbc       des-ede-cfb       des-ede-ofb
    des-ede3          des-ede3-cbc      des-ede3-cfb      des-ede3-ofb
    des-ofb           des3              desx              rc2
    rc2-40-cbc        rc2-64-cbc        rc2-cbc           rc2-cfb
    rc2-ecb           rc2-ofb           rc4               rc4-40
    seed              seed-cbc          seed-cfb          seed-ecb
    seed-ofb          zlib


###Step 1: Generate a Private Key

####Precaution
Create a safe directory for this work, and protect your private key
```
$ mkdir -m0700 ca_private
$ cd ca_private
$ touch fd.key
$ chmod 0600 fd.key
```

To generate a RSA key
```
$ openssl genrsa -aes256 -out fd.key 2048

Generating RSA private key, 2048 bit long modulus
.............................................................................................+++
............+++
e is 65537 (0x10001)
Enter pass phrase for fd.key:
Verifying - Enter pass phrase for fd.key:
```

> NOTE: 2048-bit RSA keys are considered secure, as of 2013.
> NOTE: one can also use aes128 or aes192 switch, but stay away from other algorithms, such as, des, 3des, seed, etc.

(Optional)
Private keys are stored in the so-called PEM format, which is ascii
```
$ cat fd.key

-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-256-CBC,BA43ED8A5A970F9B95A71F3F3ADEC2A6

5oUbU+FCA7ejHMc1snPQuYnzduZotCxEN9ChLXm3oFFjwYqPpQ6TloeWWnq2LaGo
[23 lines removed...]
lsLDnLkRbeKh93N7LgOifFHP0rYInT9Y+T00XCyvXBxEG2GkXuwyvqQRQ0SS7XNm
-----END RSA PRIVATE KEY-----
```

(Optional)
View a key's structure by following command
```
$ openssl rsa -text -in fd.key

Enter pass phrase for fd.key:
Private-Key: (2048 bit)
modulus:
    00:bd:05:c9:ca:71:63:59:70:e1:d6:bc:31:10:05:
    [...]
publicExponent: 65537 (0x10001)
privateExponent:
    03:11:9c:fd:14:6a:30:81:8c:a9:17:d5:14:15:83:
    [...]
prime1:
    00:e6:3e:5f:f8:d9:81:8c:da:4c:4a:e0:1a:2a:be:
    [...]
prime2:
    00:d2:2a:f1:16:10:0b:18:42:2f:14:41:b0:9d:c0:
    [...]
exponent1:
    4c:cf:0c:27:31:30:55:b3:d1:75:bd:13:1d:a5:67:
    [...]
exponent2:
    00:b1:a3:2c:0c:ca:d8:a5:d3:62:f9:8b:20:5d:e6:
    [...]
coefficient:
    00:c6:6f:0d:0d:bc:99:a8:09:f6:38:17:aa:16:b4:
    [...]
writing RSA key
-----BEGIN RSA PRIVATE KEY-----
[...]
-----END RSA PRIVATE KEY-----

```

If you need to generate the corresponding public key
```
$ openssl rsa -in fd.key -pubout -out fd-public.key
```
**TODO**
**TODO**

(Optional)
Take a peek at the public key
```
$ cat fd-public.key
```
**TODO**
**TODO**

###Step 2: Create a CSR (Certificate Signing Request)

Once the private key is generated a Certificate Signing Request can be
generated. The CSR is then used in one of two ways. Ideally, the CSR will be
sent to a Certificate Authority, such as Thawte or Verisign who will verify the
identity of the requestor and issue a signed certificate. **The second option is
to self-sign the CSR, which will be demonstrated in the next section.**

During the generation of the CSR, you will be prompted for several pieces of
information. These are the X.509 attributes of the certificate. One of the
prompts will be for "Common Name (e.g., YOUR name)". It is important that this
field be filled in with the fully qualified domain name of the server to be
protected by SSL. If the website to be protected will be
https://public.akaiia.com, then enter public.akadia.com at this prompt. The
command to generate the CSR is as follows:

```
$ openssl req -new -key fd.key -out fd.csr

Enter pass phrase for fd.key:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:CA
State or Province Name (full name) [Some-State]:.
Locality Name (eg, city) []:.
Organization Name (eg, company) [Internet Widgits Pty Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (e.g. server FQDN or YOUR name) []:localhost
Email Address []:.

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

```

> NOTE: According to section 5.4.1 of RFC 2985, challenge password is an optional
> field that was intended for use during certiificate revocation as a way of
> identifying the original entity that had requested the certificate. If entered,
> the password will be included verbatim in the CSR and communicated to the CA.
> It’s actually quite rare to find a CA that relies on this field, however. All
> instructions I’ve seen recommend leaving it alone. Having a challenge password
> does not increase the security of the CSR in any way. Further, this field
> should not be confused with the key passphrase, which is a separate feature.


###Step 3: Remove Passphrase from Key 

<https://www.madboa.com/geek/openssl/#key-removepass>

- Remove passphase from an RSA key, will be prompted for passphase one last time
```
$ cp fd.key fd0.key
$ openssl rsa -in fd0.key -out fd.key
```


###Step 4: Generating a Self-Signed Certificate

Double check that the CSR is correct

```
$ openssl req -text -in fd.csr -noout
Certificate Request:
    Data:
        Version: 0 (0x0)
        Subject: C=CA, CN=localhost
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:bd:05:c9:ca:71:63:59:70:e1:d6:bc:31:10:05:
                    [...]
                    d2:3d
                Exponent: 65537 (0x10001)
        Attributes:
            a0:00
    Signature Algorithm: sha1WithRSAEncryption
         bb:0d:3f:f6:ab:bf:9e:12:ce:f2:83:2d:5c:92:c8:72:6b:9f:
        [...]
         a8:52:1d:59
```

(Optional)
You can save yourself some typing if you’re renewing a certiﬁcate and don’t
want to make anyanges to the information presented in it. With the following
command, you can create a brand-new CSR from an existing certiﬁcate:

```
$ openssl x509 -x509toreq -in fd.crt -out fd.csr -signkey fd.key
```

> NOTE: Unless you’re using some form of public key pinning and wish to continue
> using the existing key, it’s best practice to generate a new key every time you
> apply for a new certiﬁcate. Key generation is quick and inexpensive and reduces
> your exposure.


###Unattended CSR Generation

###Signing Your Own Certificates (without going to a CA)

If you’re installing an SSL server for your own use, you probably don’t want to
go to a CA to a publicly trusted certiﬁcate. It’s much easier to sign your own.
The fastest way to do this is to generate a self-signed certiﬁcate. If you’re a
Firefox user, on your ﬁrst visit to the web site you can create a certiﬁcate
exception, after which the site will be as secure as if it wereted with a
publicly trusted certiﬁcate. 

**The Real Deal**
If you already have a CSR, create a certiﬁcate using the following command:

```
$ openssl x509 -req -days 365 -in fd.csr -signkey fd.key -out fd.crt

Signature ok
subject=/C=CA/CN=localhost
Getting Private key
Enter pass phrase for fd.key:
```

You don’t actually have to create a CSR in a separate step. The following
command creates a **self-signed certiﬁcate** starting with a key alone:

**The real deal in one line**

```    
$ openssl req -new -x509 -days 365 -key fd.key -out fd.crt
```

###Creating Certificates Valid for Multiple Hostnames

###Examining Certificates

Flags
- text: print certificate contents
- noout: reduce clutter by not printing the encoded certificate

```
$ openssl x509 -text -in fd.crt -noout

Certificate:
    Data:
        Version: 1 (0x0)
        Serial Number: 13517071091141098623 (0xbb96488a772abc7f)
    Signature Algorithm: sha1WithRSAEncryption
        Issuer: C=CA, CN=localhost
        Validity
            Not Before: Aug 28 04:10:05 2014 GMT
            Not After : Aug 25 04:10:05 2024 GMT
        Subject: C=CA, CN=localhost
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:bd:05:c9:ca:71:63:59:70:e1:d6:bc:31:10:05:
                    [...]
                    d2:3d
                Exponent: 65537 (0x10001)
    Signature Algorithm: sha1WithRSAEncryption
         8f:3d:92:fb:36:48:ed:70:75:af:5d:0e:90:b0:3c:79:ba:37:
        [...]
         ef:51:42:63
```

###Step 5: Installing the Private Key and Certificate

