Resources
=========
*   <https://help.ubuntu.com/community/GnuPrivacyGuardHowto>
*   <http://www.gnupg.org/documentation/howtos.html>
*   <https://wiki.archlinux.org/index.php/GnuPG>
*   Analysing PGP Identities
    <http://www.macfreek.nl/memory/Analysing_PGP_Identities>
*   Convert GPG keys to subkeys
    <http://www.macfreek.nl/memory/Convert_GPG_keys_to_subkeys>
*   HOW TO MIGRATE A (SUB)KEY INTO A NEW KEY
    <http://atom.smasher.org/gpg/gpg*migrate.txt>
*   Creating the Perfect GPG Keypair
    <https://alexcabal.com/creating*the-perfect-gpg-keypair/>
*   command line self-help
    --help
    --dump-options


[-k|--list-keys]

# detail list keys
$ gpg --edit-key userid fpr quit

--import <public-key-file>
$ gpg --import pubkey.txt

--verify <sig-key-file>
$ gpg --verify file.{asc,sig}

[-s|--sign]             # Make a signature
[-b|--detach-sign]      # Make a detached signature
$ gpg -s <file>
$ gpg -sb <file>        # sign and detach
$ gpg -b <ciphertextfile>

--clearsign             # make a clear text signature
$ gpg --clearsign <file>

[-c|--symmetric]
#  Encrypt with a symmetric cipher using a passphrase. The default symmetric
#+ cipher used is CAST5, but may be chosen with the --cipher-algo option.
$ gpg -c file           # encrypt; passphrase will be asked twice
$ gpg file.gpg          # decrypt

[-a|--armor]
# Create ASCII armored output. The default is to create the binary OpenPGP format.
$ gpg -c -a file        # encrypted file will be in ascii format
$ gpg file.asc          # decrypt

--enarmor       # Input binary, output ASCII
--dearmor       # Input ASCII, output binary

--print-md <hash-algo> <file>   # checksum with different algos
$ gpg --print-md sha1   file
$ gpg --print-md md5    file
$ gpg --print-md sha512 file

#  This hack generates 16 random binary bytes using GnuPG
#+ then converts them to base64, again using GnuPG.
#+ The final sed command strips out the headers leaving a single line
#+ that can be used as a passphrase
$ gpg --gen-random --armor 1 16
$ gpg --gen-random 1 16 | gpg --enarmor | sed -n 5p

#  Using GPG to compress tarballs. The tarball will end up
#+ being about the same size, but with added encryption
$ tar -cf - these files here | gpg -c > these-files-here.tgp    # Compress and encrypt
$ gpg < these-files-here.tgp  | tar -xvf -                      # Decrypt and Uncompress


The following commands are from
https://kb.iu.edu/data/awiu.html

>>> Encryption/decryption commands <<<

To encrypt a plaintext file with the recipient's public key:
$ gpg -e -r recipient_userid textfile

To sign a plaintext file with your secret key:
$ gpg -s textfile

To sign a plaintext file with your secret key and have the output readable to people without running GPG first:
$ gpg --clearsign textfile

To sign a plaintext file with your secret key, and then encrypt it with the recipient's public key:
$ gpg -se -r recipient_userid

To decrypt an encrypted file, or to check the signature integrity of a signed file:
$ gpg [-o outputfile] ciphertextfile

>>> Key management commands <<<

To generate your own unique public/secret key pair:
$ gpg --gen-key

To add a public or secret key file's contents to your public or secret key ring:
$ gpg --import keyfile

To extract (copy) a key from your public or secret key ring:
$ gpg -ao keyfile --export userid
or
$ gpg -ao keyfile --export-secret-key

To view the contents of your public key ring:
$ gpg --list-keys

To view the "fingerprint" of a public key, to help verify it over the telephone with its owner:
$ gpg --fingerprint userid

To view the contents and check the certifying signatures of your public key ring:
$ gpg --check-sigs

To edit a key:
$ gpg --edit-key userid

To remove a key or just a userid from your public key ring:
$ gpg --delete-key userid

To permanently revoke your own key, issuing a key compromise certificate:
$ gpg --gen-revoke userid

To disable or re-enable a public key on your own public key ring:
$ gpg --batch --edit-key userid disable
or
$ gpg --batch -edit-key userid enable

>>> Esoteric commands <<<

To create a signature certificate that is detached from the document:
$ gpg -sb textfile

To detach a signature certificate from a signed message
$ gpg -b ciphertextfile

>>> Import/Export keys <<<

Export public key
$ gpg --export --armor --output pubkey.asc userid
$ gpg --export -a -o pubkey.asc userid
$ gpg --export --output pubkey.gpg userid
$ gpg --export -o pubkey.gpg userid

Export secret key
$ gpg --export-secret-keys -o seckey.gpg userid

Export secret subkey
$ gpg --export-secret-subkeys -o ssbkey.gpg userid

Import public/secret/secretsub key
$ gpg --import keyfile

Generate revoke certificate
$ gpg --gen-revoke userid > revoke.asc

!!!! setpref !!!!
# https://alexcabal.com/creating-the-perfect-gpg-keypair/

*****

# Correctly Generating a GPG keyset in 8 steps 

**Goal: Create a signing master key, which signs a pair of subkeys for encryption and signature**

Resources
* `man gpg`
* <http://www.macfreek.nl/memory/Convert_GPG_keys_to_subkeys>
* <https://alexcabal.com/creating-the-perfect-gpg-keypair/>

## Step 1 - Generate Master Key (RSA sign only, 4096 bits, never expire)

$ gpg --gen-key

    Please select what kind of key you want:
       (1) RSA and RSA (default)
       (2) DSA and Elgamal
       (3) DSA (sign only)
       (4) RSA (sign only)

Your selection? 4

    RSA keys may be between 1024 and 4096 bits long.

What keysize do you want? (2048) 4096

    Requested keysize is 4096 bits
    Please specify how long the key should be valid.
        0 = key does not expire
        <n>  = key expires in n days
        <n>w = key expires in n weeks
        <n>m = key expires in n months
        <n>y = key expires in n years

Key is valid for? (0) 0

    Key does not expire at all

Is this correct? (y/N) y

    GnuPG needs to construct a user ID to identify your key.

Real name: realname
Email address: email@address.tld
Comment:

    You selected this USER-ID:
    "realname <email@address.tld>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o

    You need a Passphrase to protect your secret key.

    We need to generate a lot of random bytes. It is a good idea to perform
    some other action (type on the keyboard, move the mouse, utilize the
    disks) during the prime generation; this gives the random number
    generator a better chance to gain enough entropy.
    gpg: key F356C161 marked as ultimately trusted
    public and secret key created and signed.

    gpg: checking the trustdb
    gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
    gpg: depth: 0  valid:   6  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 6u
    gpg: next trustdb check due at 2017-04-04
    pub   4096R/F356C161 2014-10-21
    Key fingerprint = 922D 3DDE 2F07 78BC 7DA0  0363 4036 CEAF F356 C161
    uid       [ultimate] realname <email@address.tld>

    Note that this key cannot be used for encryption.  You may want to use
    the command "--edit-key" to generate a subkey for this purpose.


## Step 2- Strengthening hash preferences

$ gpg --edit-key F356C161

    Secret key is available.

    pub  4096R/F356C161  created: 2014-10-21  expires: never       usage: SC
                         trust: ultimate      validity: ultimate
    [ultimate] (1). realname <email@address.tld>

gpg> setpref SHA512 SHA384 SHA256 SHA224 AES256 AES192 AES TWOFISH CAMELLIA256 CAMELLIA192 CAMELLIA128 CAST5 BZIP2 ZLIB ZIP Uncompressed

    Set preference list to:
         Cipher: AES256, AES192, AES, TWOFISH, CAMELLIA256, CAMELLIA192, CAMELLIA128, CAST5, 3DES
         Digest: SHA512, SHA384, SHA256, SHA224, SHA1
         Compression: BZIP2, ZLIB, ZIP, Uncompressed
         Features: MDC, Keyserver no-modify

Really update the preferences? (y/N) y

    You need a passphrase to unlock the secret key for
    user: "realname <email@address.tld>"
    4096-bit RSA key, ID F356C161, created 2014-10-21


    pub  4096R/F356C161  created: 2014-10-21  expires: never       usage: SC
                         trust: ultimate      validity: ultimate
    [ultimate] (1). realname <email@address.tld>


## Step 3 - Add a Signing Subkey (RSA, 4096 bits, expires in 2y)

gpg> addkey

    Key is protected.

    You need a passphrase to unlock the secret key for
    user: "realname <email@address.tld>"
    4096-bit RSA key, ID F356C161, created 2014-10-21

    Please select what kind of key you want:
       (3) DSA (sign only)
       (4) RSA (sign only)
       (5) Elgamal (encrypt only)
       (6) RSA (encrypt only)

Your selection? 4

    RSA keys may be between 1024 and 4096 bits long.
    What keysize do you want? (2048) 4096
    Requested keysize is 4096 bits
    Please specify how long the key should be valid.
             0 = key does not expire
          <n>  = key expires in n days
          <n>w = key expires in n weeks
          <n>m = key expires in n months
          <n>y = key expires in n years

Key is valid for? (0) 2y

    Key expires at Thu 20 Oct 2016 01:10:54 PM HKT

Is this correct? (y/N) y
Really create? (y/N) y

    We need to generate a lot of random bytes. It is a good idea to perform
    some other action (type on the keyboard, move the mouse, utilize the
    disks) during the prime generation; this gives the random number
    generator a better chance to gain enough entropy.

    pub  4096R/F356C161  created: 2014-10-21  expires: never       usage: SC
                         trust: ultimate      validity: ultimate
    sub  4096R/3A8B279A  created: 2014-10-21  expires: 2016-10-20  usage: S
    [ultimate] (1). realname <email@address.tld>


## Step 4 - Add an Encryption Subkey (RSA, 4096 bits, expires in 2y)

gpg> addkey

    Key is protected.

    You need a passphrase to unlock the secret key for
    user: "realname <email@address.tld>"
    4096-bit RSA key, ID F356C161, created 2014-10-21

    Please select what kind of key you want:
       (3) DSA (sign only)
       (4) RSA (sign only)
       (5) Elgamal (encrypt only)
       (6) RSA (encrypt only)

Your selection? 6

    RSA keys may be between 1024 and 4096 bits long.

What keysize do you want? (2048) 4096

    Requested keysize is 4096 bits
    Please specify how long the key should be valid.
             0 = key does not expire
          <n>  = key expires in n days
          <n>w = key expires in n weeks
          <n>m = key expires in n months
          <n>y = key expires in n years

Key is valid for? (0) 2y

    Key expires at Thu 20 Oct 2016 01:11:07 PM HKT

Is this correct? (y/N) y
Really create? (y/N) y

    We need to generate a lot of random bytes. It is a good idea to perform
    some other action (type on the keyboard, move the mouse, utilize the
    disks) during the prime generation; this gives the random number
    generator a better chance to gain enough entropy.

    pub  4096R/F356C161  created: 2014-10-21  expires: never       usage: SC
                         trust: ultimate      validity: ultimate
    sub  4096R/3A8B279A  created: 2014-10-21  expires: 2016-10-20  usage: S
    sub  4096R/4DFD06B5  created: 2014-10-21  expires: 2016-10-20  usage: E
    [ultimate] (1). realname <email@address.tld>

gpg> save

## Step 5 - Generate a Revocation Certificate

$ mkdir f356c161 && cd f356c161
$ gpg --gen-revoke F356C161 > revoke.asc

    sec  4096R/F356C161 2014-10-21 realname <email@address.tld>

Create a revocation certificate for this key? (y/N) y

    Please select the reason for the revocation:
      0 = No reason specified
      1 = Key has been compromised
      2 = Key is superseded
      3 = Key is no longer used
      Q = Cancel
    (Probably you want to select 1 here)

Your decision? 0

    Enter an optional description; end it with an empty line:
    >
    Reason for revocation: No reason specified
    (No description given)

Is this okay? (y/N) y

    You need a passphrase to unlock the secret key for
    user: "realname <email@address.tld>"
    4096-bit RSA key, ID F356C161, created 2014-10-21

    ASCII armored output forced.
    Revocation certificate created.

    Please move it to a medium which you can hide away; if Mallory gets
    access to this certificate he can use it to make your key unusable.
    It is smart to print this certificate and store it away, just in case
    your media become unreadable.  But have some caution:  The print system of
    your machine might store the data and make it available to others!


## Step 6 - Export Public Key/Secret Key/Secret Subkeys to an Offline medium

$ gpg --armor --export --output public.asc F356C161
$ gpg --export-secret-keys --output private.gpg F356C161
$ gpg --export-secret-subkeys --output subkeys.gpg F356C161
$ ls

    private.gpg  public.asc  revoke.asc  subkeys.gpg

    
## Step 7 - Delete Secret Keys from Keyring and then ONLY import back the (Secret-)Subkeys

$ gpg -K

    /home/realname/.gnupg/secring.gpg
    ----------------------------
    sec   4096R/F356C161 2014-10-21
    uid                  realname <email@address.tld>
    ssb   4096R/3A8B279A 2014-10-21
    ssb   4096R/4DFD06B5 2014-10-21

$ gpg --delete-secret-key F356C161

    sec  4096R/F356C161 2014-10-21 realname <email@address.tld>

Delete this key from the keyring? (y/N) y
This is a secret key! - really delete? (y/N) y


$ ls

    private.gpg  public.asc  revoke.asc  subkeys.gpg

$ gpg --import subkeys.gpg

    gpg: key F356C161: secret key imported
    gpg: key F356C161: "realname <email@address.tld>" 1 new signature
    gpg: Total number processed: 1
    gpg:         new signatures: 1
    gpg:       secret keys read: 1
    gpg:   secret keys imported: 1


## Step 8 - Done. Check Result.

$ gpg -K

    /home/realname/.gnupg/secring.gpg
    ----------------------------
    sec#  4096R/F356C161 2014-10-21
    uid                  realname <email@address.tld>
    ssb   4096R/3A8B279A 2014-10-21
    ssb   4096R/4DFD06B5 2014-10-21


