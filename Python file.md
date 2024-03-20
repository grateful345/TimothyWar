![image](https://github.com/grateful345/TimothyWar/assets/163609752/cc1b6bf4-6b28-4d78-9bdb-6577d0fdc363)


--import option.

alice% gpg --import blake.gpg
gpg: key 9E98BC16: public key imported
gpg: Total number processed: 1
gpg:               imported: 1
alice% gpg --list-keys
/users/alice/.gnupg/pubring.gpg
---------------------------------------
pub  1024D/BB7576AC 1999-06-04 Alice (Judge) <alice@cyb.org>
sub  1024g/78E9A8FA 1999-06-04

pub  1024D/9E98BC16 1999-06-04 Blake (Executioner) <blake@cyb.org>
sub  1024g/5C8CBD41 1999-06-04
Once a key is imported it should be validated. GnuPG uses a powerful and flexible trust model that does not require you to personally validate each key you import. Some keys may need to be personally validated, however. A key is validated by verifying the key's fingerprint and then signing the key to certify it as a valid key. A key's fingerprint can be quickly viewed with the --fingerprint command-line option, but in order to certify the key you must edit it.

alice% gpg --edit-key blake@cyb.org

pub  1024D/9E98BC16  created: 1999-06-04 expires: never      trust: -/q
sub  1024g/5C8CBD41  created: 1999-06-04 expires: never     
(1)  Blake (Executioner) <blake@cyb.org>

Command> fpr
pub  1024D/9E98BC16 1999-06-04 Blake (Executioner) <blake@cyb.org>
             Fingerprint: 268F 448F CCD7 AF34 183E  52D8 9BDE 1A08 9E98 BC16
A key's fingerprint is verified with the key's owner. This may be done in person or over the phone or through any other means as long as you can guarantee that you are communicating with the key's true owner. If the fingerprint you get is the same as the fingerprint the key's owner gets, then you can be sure that you have a correct copy of the key.
After checking the fingerprint, you may sign the key to validate it. Since key verification is a weak point in public-key cryptography, you should be extremely careful and always check a key's fingerprint with the owner before signing the key.

Command> sign
             
pub  1024D/9E98BC16  created: 1999-06-04 expires: never      trust: -/q
             Fingerprint: 268F 448F CCD7 AF34 183E  52D8 9BDE 1A08 9E98 BC16

     Blake (Executioner) <blake@cyb.org>

Are you really sure that you want to sign this key
with your key: "Alice (Judge) <alice@cyb.org>"

Really sign?
Once signed you can check the key to list the signatures on it and see the signature that you have added. Every user ID on the key will have one or more self-signatures as well as a signature for each user that has validated the key.

Command> check
uid  Blake (Executioner) <blake@cyb.org>
sig!       9E98BC16 1999-06-04   [self-signature]
sig!       BB7576AC 1999-06-04   Alice (Judge) <alice@cyb.org>

--list-keys.

alice% gpg --list-keys
/users/alice/.gnupg/pubring.gpg
---------------------------------------
pub  1024D/BB7576AC 1999-06-04 Alice (Judge) <alice@cyb.org>
sub  1024g/78E9A8FA 1999-06-04
Exporting a public key

To send your public key to a correspondent you must first export it. The command-line option --export is used to do this. It takes an additional argument identifying the public key to export. As with the --gen-revoke option, either the key ID or any part of the user ID may be used to identify the key to export.

alice% gpg --output alice.gpg --export alice@cyb.org
The key is exported in a binary format, but this can be inconvenient when the key is to be sent though email or published on a web page. GnuPG therefore supports a command-line option --armor[2] that causes output to be generated in an ASCII-armored format similar to uuencoded documents. In general, any output from GnuPG, e.g., keys, encrypted documents, and signatures, can be ASCII-armored by adding the --armor option.

alice% gpg --armor --export alice@cyb.org
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v0.9.7 (GNU/Linux)
Comment: For info see http://www.gnupg.org

[...]
-----END PGP PUBLIC KEY BLOCK-----

alice% gpg --output doc.gpg --encrypt --recipient blake@cyb.org doc
The --recipient option is used once for each recipient and takes an extra argument specifying the public key to which the document should be encrypted. The encrypted document can only be decrypted by someone with a private key that complements one of the recipients' public keys. In particular, you cannot decrypt a document encrypted by you unless you included your own public key in the recipient list.
To decrypt a message the option --decrypt is used. You need the private key to which the message was encrypted. Similar to the encryption process, the document to decrypt is input, and the decrypted result is output.

blake% gpg --output doc --decrypt doc.gpg

You need a passphrase to unlock the secret key for
user: "Blake (Executioner) <blake@cyb.org>"
1024-bit ELG-E key, ID 5C8CBD41, created 1999-06-04 (main key ID 9E98BC16)

Enter passphrase: 
Documents may also be encrypted without using public-key cryptography. Instead, you use a symmetric cipher to encrypt the document. The key used to drive the symmetric cipher is derived from a passphrase supplied when the document is encrypted, and for good security, it should not be the same passphrase that you use to protect your private key. Symmetric encryption is useful for securing documents when the passphrase does not need to be communicated to others. A document can be encrypted with a symmetric cipher by using the --symmetric option.

alice% gpg --output doc.gpg --symmetric doc
Enter passphrase: gpg --encrypt --recipient 05D02D3D57ABFF46 FILENAME

alice% gpg --output doc.sig --sign doc

You need a passphrase to unlock the private key for
user: "Alice (Judge) <alice@cyb.org>"
1024-bit DSA key, ID BB7576AC, created 1999-06-04

Enter passphrase: 
The document is compressed before being signed, and the output is in binary format.
Given a signed document, you can either check the signature or check the signature and recover the original document. To check the signature use the --verify option. To verify the signature and extract the document use the --decrypt option. The signed document to verify and recover is input and the recovered document is output.

blake% gpg --output doc --decrypt doc.sig
gpg: Signature made Fri Jun  4 12:02:38 1999 CDT using DSA key ID BB7576AC
gpg: Good signature from "Alice (Judge) <alice@cyb.org>"
alice% gpg --clearsign doc

You need a passphrase to unlock the secret key for
user: "Alice (Judge) <alice@cyb.org>"
1024-bit DSA key, ID BB7576AC, created 1999-06-04

-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA1

[...]
-----BEGIN PGP SIGNATURE-----
Version: GnuPG v0.9.7 (GNU/Linux)
Comment: For info see http://www.gnupg.org

iEYEARECAAYFAjdYCQoACgkQJ9S6ULt1dqz6IwCfQ7wP6i/i8HhbcOSKF4ELyQB1
oCoAoOuqpRqEzr4kOkQqHRLE/b8/Rw2k
=y6kj
-----END PGP SIGNATURE-----

option --edit-key may be used to view a keypair. For example,

chloe% gpg --edit-key chloe@cyb.org
Secret key is available.

pub  1024D/26B6AAE1  created: 1999-06-15 expires: never      trust: -/u
sub  2048g/0CF8CB7A  created: 1999-06-15 expires: never
sub  1792G/08224617  created: 1999-06-15 expires: 2002-06-14
sub   960D/B1F423E7  created: 1999-06-15 expires: 2002-06-14
(1)  Chloe (Jester) <chloe@cyb.org>
(2)  Chloe (Plebian) <chloe@tel.net>
Command>
The public key is displayed along with an indication of whether or not the private key is available. Information about each component of the public key is then listed. The first column indicates the type of the key. The keyword pub identifies the public master signing key, and the keyword sub identifies a public subordinate key. The second column indicates the key's bit length, type, and ID. The type is D for a DSA key, g for an encryption-only ElGamal key, and G for an ElGamal key that may be used for both encryption and signing. The creation date and expiration date are given in columns three and four. The user IDs are listed following the keys.
More information about the key can be obtained with interactive commands. The command toggle switches between the public and private components of a keypair if indeed both components are available.

Command> toggle

sec  1024D/26B6AAE1  created: 1999-06-15 expires: never
sbb  2048g/0CF8CB7A  created: 1999-06-15 expires: never
sbb  1792G/08224617  created: 1999-06-15 expires: 2002-06-14
sbb   960D/B1F423E7  created: 1999-06-15 expires: 2002-06-14
(1)  Chloe (Jester) <chloe@cyb.org>
(2)  Chloe (Plebian) <chloe@tel.net>

chloe% gpg --edit-key chloe
Secret key is available.

pub  1024D/26B6AAE1  created: 1999-06-15 expires: never      trust: -/u
sub  2048g/0CF8CB7A  created: 1999-06-15 expires: never
sub  1792G/08224617  created: 1999-06-15 expires: 2002-06-14
sub   960D/B1F423E7  created: 1999-06-15 expires: 2002-06-14
(1)  Chloe (Jester) <chloe@cyb.org>
(2)  Chloe (Plebian) <chloe@tel.net>

Command> check
uid  Chloe (Jester) <chloe@cyb.org>
sig!	   26B6AAE1 1999-06-15	 [self-signature]
uid  Chloe (Plebian) <chloe@tel.net>
sig!	   26B6AAE1 1999-06-15	 [self-signature]
As expected, the signing key for each signature is the master signing key with key ID 0x26B6AAE1. 
alice% gpg --edit-key blake

pub  1024D/8B927C8A  created: 1999-07-02 expires: never      trust: q/f
sub  1024g/C19EA233  created: 1999-07-02 expires: never
(1)  Blake (Executioner) <blake@cyb.org>

Command> trust
pub  1024D/8B927C8A  created: 1999-07-02 expires: never      trust: q/f
sub  1024g/C19EA233  created: 1999-07-02 expires: never
(1)  Blake (Executioner) <blake@cyb.org>

Please decide how far you trust this user to correctly
verify other users' keys (by looking at passports,
checking fingerprints from different sources...)?

 1 = Don't know
 2 = I do NOT trust
 3 = I trust marginally
 4 = I trust fully
 s = please show me more information
 m = back to the main menu

Your decision? 3

pub  1024D/8B927C8A  created: 1999-07-02 expires: never      trust: m/f
sub  1024g/C19EA233  created: 1999-07-02 expires: never
(1)  Blake (Executioner) <blake@cyb.org>

Command> quit
[...]
 --recv-keys is used to retrieve keys from a keyserver, but the option --recv-keys requires a key ID be used to specify the key. In the following example Alice updates her public key with new signatures from the keyserver certserver.pgp.com and then sends her copy of Blake's public key to the same keyserver to contribute any new signatures she may have added.

alice% gpg --keyserver certserver.pgp.com --recv-key 0xBB7576AC
gpg: requesting key BB7576AC from certserver.pgp.com ...
gpg: key BB7576AC: 1 new signature

gpg: Total number processed: 1
gpg:	     new signatures: 1
alice% gpg --keyserver certserver.pgp.com --send-key blake@cyb.org
gpg: success sending to 'certserver.pgp.com' (status=200)


file.md

-----BEGIN PGP PUBLIC KEY BLOCK-----

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
pVsjxpylBcSjsPE8MAki7Hb2Rw==
=syRC
-----END PGP PUBLIC KEY BLOCK-----
---
 Python file.md | 181 +++++++++++++++++++++++++++++++++++++++++++++++++
 1 file changed, 181 insertions(+)

diff --git a/Python file.md b/Python file.md
index 706c3cb..3c545a4 100644
--- a/Python file.md	
+++ b/Python file.md	
@@ -697,3 +697,184 @@ make lint
 The library uses Black for code formatting. Code must be formatted with Black before PRs are submitted, otherwise CI will fail. Run the formatter with:
 
 make fmt
+
+pub   rsa3072 2017-03-17 [SC] [expires: 2027-03-15]
+      5B80 C575 4298 F0CB 55D8  ED6A BCEF 7E29 4B09 2E28
+uid   Andre Heinecke (Release Signing Key)
+
+pub   ed25519 2020-08-24 [SC] [expires: 2030-06-30]
+      6DAA 6E64 A76D 2840 571B  4902 5288 97B8 2640 3ADA
+uid   Werner Koch (dist signing 2020)
+
+pub   ed25519 2021-05-19 [SC] [expires: 2027-04-04]
+      AC8E 115B F73E 2D8D 47FA  9908 E98E 9B2D 19C6 C8BD
+uid   Niibe Yutaka (GnuPG Release Key)
+
+pub   brainpoolP256r1 2021-10-15 [SC] [expires: 2029-12-31]
+      02F3 8DFF 731F F97C B039  A1DA 549E 695E 905B A208
+uid   GnuPG.com (Release Signing Key 2021)
+Below is a public key block with the above keys; you may also download these keys from: https://gnupg.org/signature_key.asc
+
+-----BEGIN PGP PUBLIC KEY BLOCK-----
+
+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
+eiInsGZ/+h5XYrpXTgA=
+=4+Sn
+-----END PGP PUBLIC KEY BLOCK-----
+Former Signature Keys
+Releases done in the years 2006 to 2010 were signed by this key:
+
+pub   rsa1024/1CE0C630 2006-01-01 [expired: 2011-06-30]
+      Key fingerprint = 7B96 D396 E647 1601 754B  E4DB 53B6 20D0 1CE0 C630
+uid   [ expired] Werner Koch (dist sig) <dd9jn@gnu.org>
+Releases done in the years 1998 to 2005 were signed by this key:
+
+pub   dsa1024/57548DCD 1998-07-07 [expired: 2005-12-31]
+      Key fingerprint = 6BD9 050F D8FC 941B 4341  2DCC 68B7 AB89 5754 8DCD
+uid   [ expired] Werner Koch (gnupg sig) <dd9jn@gnu.org>
+For reference a public key block with expired keys used to sign older releases can be found here.
+
+-----BEGIN PGP PUBLIC KEY BLOCK-----
+
+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
+pVsjxpylBcSjsPE8MAki7Hb2Rw==
+=syRC
+-----END PGP PUBLIC KEY BLOCK-----

alice% gpg --gen-key
gpg (GnuPG) 0.9.4; Copyright (C) 1999 Free Software Foundation, Inc.
This program comes with ABSOLUTELY NO WARRANTY.
This is free software, and you are welcome to redistribute it
under certain conditions. See the file COPYING for details.

Please select what kind of key you want:
   (1) DSA and ElGamal (default)
   (2) DSA (sign only)
   (4) ElGamal (sign and encrypt)
Your selection?
GnuPG is able to create several different types of keypairs, but a primary key must be capable of making signatures. There are therefore only three options. Option 1 actually creates two keypairs. A DSA keypair is the primary keypair usable only for making signatures. An ElGamal subordinate keypair is also created for encryption. Option 2 is similar but creates only a DSA keypair. Option 4[1] creates a single ElGamal keypair usable for both making signatures and performing encryption. In all cases it is possible to later add additional subkeys for encryption and signing. For most users the default option is fine.
You must also choose a key size. The size of a DSA key must be between 512 and 1024 bits, and an ElGamal key may be of any size. GnuPG, however, requires that keys be no smaller than 768 bits. Therefore, if Option 1 was chosen and you choose a keysize larger than 1024 bits, the ElGamal key will have the requested size, but the DSA key will be 1024 bits.

About to generate a new ELG-E keypair.
              minimum keysize is  768 bits
              default keysize is 1024 bits
    highest suggested keysize is 2048 bits
What keysize do you want? (1024)
The longer the key the more secure it is against brute-force attacks, but for almost all purposes the default keysize is adequate since it would be cheaper to circumvent the encryption than try to break it. Also, encryption and decryption will be slower as the key size is increased, and a larger keysize may affect signature length. Once selected, the keysize can never be changed.
Finally, you must choose an expiration date. If Option 1 was chosen, the expiration date will be used for both the ElGamal and DSA keypairs.

Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 
For most users a key that does not expire is adequate. The expiration time should be chosen with care, however, since although it is possible to change the expiration date after the key is created, it may be difficult to communicate a change to users who have your public key.
You must provide a user ID in addition to the key parameters. The user ID is used to associate the key being created with a real person.

You need a User-ID to identify your key; the software constructs the user id
from Real Name, Comment and Email Address in this form:
    "Heinrich Heine (Der Dichter) <heinrichh@duesseldorf.de>"

Real name: 
Only one user ID is created when a key is created, but it is possible to create additional user IDs if you want to use the key in two or more contexts, e.g., as an employee at work and a political activist on the side. A user ID should be created carefully since it cannot be edited after it is created.
GnuPG needs a passphrase to protect the primary and subordinate private keys that you keep in your possession.

You need a Passphrase to protect your private key.    

Enter passphrase: 
There is no limit on the length of a passphrase, and it should be carefully chosen. From the perspective of security, the passphrase to unlock the private key is one of the weakest points in GnuPG (and other public-key encryption systems as well) since it is the only protection you have if another individual gets your private key. Ideally, the passphrase should not use words from a dictionary and should mix the case of alphabetic characters as well as use non-alphabetic characters. A good passphrase is crucial to the secure use of GnuPG.
Generating a revocation certificate

After your keypair is created you should immediately generate a revocation certificate for the primary public key using the option --gen-revoke. If you forget your passphrase or if your private key is compromised or lost, this revocation certificate may be published to notify others that the public key should no longer be used. A revoked public key can still be used to verify signatures made by you in the past, but it cannot be used to encrypt future messages to you. It also does not affect your ability to decrypt messages sent to you in the past if you still do have access to the private key.

alice% gpg --output revoke.asc --gen-revoke mykey
[...]
The argument mykey must be a key specifier, either the key ID of your primary keypair or any part of a user ID that identifies your keypair. The generated certificate will be left in the file revoke.asc. If the --output option is omitted, the result will be placed on standard output. Since the certificate is short, you may wish to print a hardcopy of the certificate to store somewhere safe such as your safe deposit box. The certificate should not be stored where others can access it since anybody can publish the revocation certificate and render the corresponding public key useless.
Exchanging keys

To communicate with others you must exchange public keys. To list the keys on your public keyring use the command-line option --list-keys.

alice% gpg --list-keys
/users/alice/.gnupg/pubring.gpg
---------------------------------------
pub  1024D/BB7576AC 1999-06-04 Alice (Judge) <alice@cyb.org>
sub  1024g/78E9A8FA 1999-06-04
Exporting a public key

To send your public key to a correspondent you must first export it. The command-line option --export is used to do this. It takes an additional argument identifying the public key to export. As with the --gen-revoke option, either the key ID or any part of the user ID may be used to identify the key to export.

alice% gpg --output alice.gpg --export alice@cyb.org
The key is exported in a binary format, but this can be inconvenient when the key is to be sent though email or published on a web page. GnuPG therefore supports a command-line option --armor[2] that causes output to be generated in an ASCII-armored format similar to uuencoded documents. In general, any output from GnuPG, e.g., keys, encrypted documents, and signatures, can be ASCII-armored by adding the --armor option.

alice% gpg --armor --export alice@cyb.org
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v0.9.7 (GNU/Linux)
Comment: For info see http://www.gnupg.org

[...]
-----END PGP PUBLIC KEY BLOCK-----
Importing a public key

A public key may be added to your public keyring with the --import option.

alice% gpg --import blake.gpg
gpg: key 9E98BC16: public key imported
gpg: Total number processed: 1
gpg:               imported: 1
alice% gpg --list-keys
/users/alice/.gnupg/pubring.gpg
---------------------------------------
pub  1024D/BB7576AC 1999-06-04 Alice (Judge) <alice@cyb.org>
sub  1024g/78E9A8FA 1999-06-04

pub  1024D/9E98BC16 1999-06-04 Blake (Executioner) <blake@cyb.org>
sub  1024g/5C8CBD41 1999-06-04
Once a key is imported it should be validated. GnuPG uses a powerful and flexible trust model that does not require you to personally validate each key you import. Some keys may need to be personally validated, however. A key is validated by verifying the key's fingerprint and then signing the key to certify it as a valid key. A key's fingerprint can be quickly viewed with the --fingerprint command-line option, but in order to certify the key you must edit it.

alice% gpg --edit-key blake@cyb.org

pub  1024D/9E98BC16  created: 1999-06-04 expires: never      trust: -/q
sub  1024g/5C8CBD41  created: 1999-06-04 expires: never     
(1)  Blake (Executioner) <blake@cyb.org>

Command> fpr
pub  1024D/9E98BC16 1999-06-04 Blake (Executioner) <blake@cyb.org>
             Fingerprint: 268F 448F CCD7 AF34 183E  52D8 9BDE 1A08 9E98 BC16
A key's fingerprint is verified with the key's owner. This may be done in person or over the phone or through any other means as long as you can guarantee that you are communicating with the key's true owner. If the fingerprint you get is the same as the fingerprint the key's owner gets, then you can be sure that you have a correct copy of the key.
After checking the fingerprint, you may sign the key to validate it. Since key verification is a weak point in public-key cryptography, you should be extremely careful and always check a key's fingerprint with the owner before signing the key.

Command> sign
             
pub  1024D/9E98BC16  created: 1999-06-04 expires: never      trust: -/q
             Fingerprint: 268F 448F CCD7 AF34 183E  52D8 9BDE 1A08 9E98 BC16

     Blake (Executioner) <blake@cyb.org>

Are you really sure that you want to sign this key
with your key: "Alice (Judge) <alice@cyb.org>"

Really sign?
Once signed you can check the key to list the signatures on it and see the signature that you have added. Every user ID on the key will have one or more self-signatures as well as a signature for each user that has validated the key.

Command> check
uid  Blake (Executioner) <blake@cyb.org>
sig!       9E98BC16 1999-06-04   [self-signature]
sig!       BB7576AC 1999-06-04   Alice (Judge) <alice@cyb.org>
Encrypting and decrypting documents

A public and private key each have a specific role when encrypting and decrypting documents. A public key may be thought of as an open safe. When a correspondent encrypts a document using a public key, that document is put in the safe, the safe shut, and the combination lock spun several times. The corresponding private key is the combination that can reopen the safe and retrieve the document. In other words, only the person who holds the private key can recover a document encrypted using the associated public key.

The procedure for encrypting and decrypting documents is straightforward with this mental model. If you want to encrypt a message to Alice, you encrypt it using Alice's public key, and she decrypts it with her private key. If Alice wants to send you a message, she encrypts it using your public key, and you decrypt it with your private key.

To encrypt a document the option --encrypt is used. You must have the public keys of the intended recipients. The software expects the name of the document to encrypt as input; if omitted, it reads standard input. The encrypted result is placed on standard output or as specified using the option --output. The document is compressed for additional security in addition to encrypting it.

alice% gpg --output doc.gpg --encrypt --recipient blake@cyb.org doc
The --recipient option is used once for each recipient and takes an extra argument specifying the public key to which the document should be encrypted. The encrypted document can only be decrypted by someone with a private key that complements one of the recipients' public keys. In particular, you cannot decrypt a document encrypted by you unless you included your own public key in the recipient list.
To decrypt a message the option --decrypt is used. You need the private key to which the message was encrypted. Similar to the encryption process, the document to decrypt is input, and the decrypted result is output.

blake% gpg --output doc --decrypt doc.gpg

You need a passphrase to unlock the secret key for
user: "Blake (Executioner) <blake@cyb.org>"
1024-bit ELG-E key, ID 5C8CBD41, created 1999-06-04 (main key ID 9E98BC16)

Enter passphrase: 
Documents may also be encrypted without using public-key cryptography. Instead, you use a symmetric cipher to encrypt the document. The key used to drive the symmetric cipher is derived from a passphrase supplied when the document is encrypted, and for good security, it should not be the same passphrase that you use to protect your private key. Symmetric encryption is useful for securing documents when the passphrase does not need to be communicated to others. A document can be encrypted with a symmetric cipher by using the --symmetric option.

alice% gpg --output doc.gpg --symmetric doc
Enter passphrase: 
Making and verifying signatures

A digital signature certifies and timestamps a document. If the document is subsequently modified in any way, a verification of the signature will fail. A digital signature can serve the same purpose as a hand-written signature with the additional benefit of being tamper-resistant. The GnuPG source distribution, for example, is signed so that users can verify that the source code has not been modified since it was packaged.

Creating and verifying signatures uses the public/private keypair in an operation different from encryption and decryption. A signature is created using the private key of the signer. The signature is verified using the corresponding public key. For example, Alice would use her own private key to digitally sign her latest submission to the Journal of Inorganic Chemistry. The associate editor handling her submission would use Alice's public key to check the signature to verify that the submission indeed came from Alice and that it had not been modified since Alice sent it. A consequence of using digital signatures is that it is difficult to deny that you made a digital signature since that would imply your private key had been compromised.

The command-line option --sign is used to make a digital signature. The document to sign is input, and the signed document is output.

alice% gpg --output doc.sig --sign doc

You need a passphrase to unlock the private key for
user: "Alice (Judge) <alice@cyb.org>"
1024-bit DSA key, ID BB7576AC, created 1999-06-04

Enter passphrase: 
The document is compressed before being signed, and the output is in binary format.
Given a signed document, you can either check the signature or check the signature and recover the original document. To check the signature use the --verify option. To verify the signature and extract the document use the --decrypt option. The signed document to verify and recover is input and the recovered document is output.

blake% gpg --output doc --decrypt doc.sig
gpg: Signature made Fri Jun  4 12:02:38 1999 CDT using DSA key ID BB7576AC
gpg: Good signature from "Alice (Judge) <alice@cyb.org>"
Clearsigned documents

A common use of digital signatures is to sign usenet postings or email messages. In such situations it is undesirable to compress the document while signing it. The option --clearsign causes the document to be wrapped in an ASCII-armored signature but otherwise does not modify the document.

alice% gpg --clearsign doc

You need a passphrase to unlock the secret key for
user: "Alice (Judge) <alice@cyb.org>"
1024-bit DSA key, ID BB7576AC, created 1999-06-04

-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA1

[...]
-----BEGIN PGP SIGNATURE-----
Version: GnuPG v0.9.7 (GNU/Linux)
Comment: For info see http://www.gnupg.org

iEYEARECAAYFAjdYCQoACgkQJ9S6ULt1dqz6IwCfQ7wP6i/i8HhbcOSKF4ELyQB1
oCoAoOuqpRqEzr4kOkQqHRLE/b8/Rw2k
=y6kj
-----END PGP SIGNATURE-----
Detached signatures

A signed document has limited usefulness. Other users must recover the original document from the signed version, and even with clearsigned documents, the signed document must be edited to recover the original. Therefore, there is a third method for signing a document that creates a detached signature, which is a separate file. A detached signature is created using the --detach-sig option.

alice% gpg --output doc.sig --detach-sig doc

You need a passphrase to unlock the secret key for
user: "Alice (Judge) <alice@cyb.org>"
1024-bit DSA key, ID BB7576AC, created 1999-06-04

Enter passphrase: 
Both the document and detached signature are needed to verify the signature. The --verify option can be to check the signature.

blake% gpg --verify doc.sig doc
gpg: Signature made Fri Jun  4 12:38:46 1999 CDT using DSA key ID BB7576AC
gpg: Good signature from "Alice (Judge) <alice@cyb.org>"
Chapter 2. Concepts

GnuPG makes uses of several cryptographic concepts including symmetric ciphers, public-key ciphers, and one-way hashing. You can make basic use GnuPG without fully understanding these concepts, but in order to use it wisely some understanding of them is necessary.

This chapter introduces the basic cryptographic concepts used in GnuPG. Other books cover these topics in much more detail. A good book with which to pursue further study is Bruce Schneier's ``Applied Cryptography''.

Symmetric ciphers

A symmetric cipher is a cipher that uses the same key for both encryption and decryption. Two parties communicating using a symmetric cipher must agree on the key beforehand. Once they agree, the sender encrypts a message using the key, sends it to the receiver, and the receiver decrypts the message using the key. As an example, the German Enigma is a symmetric cipher, and daily keys were distributed as code books. Each day, a sending or receiving radio operator would consult his copy of the code book to find the day's key. Radio traffic for that day was then encrypted and decrypted using the day's key. Modern examples of symmetric ciphers include 3DES, Blowfish, and IDEA.

A good cipher puts all the security in the key and none in the algorithm. In other words, it should be no help to an attacker if he knows which cipher is being used. Only if he obtains the key would knowledge of the algorithm be needed. The ciphers used in GnuPG have this property.

Since all the security is in the key, then it is important that it be very difficult to guess the key. In other words, the set of possible keys, i.e., the key space, needs to be large. While at Los Alamos, Richard Feynman was famous for his ability to crack safes. To encourage the mystique he even carried around a set of tools including an old stethoscope. In reality, he used a variety of tricks to reduce the number of combinations he had to try to a small number and then simply guessed until he found the right combination. In other words, he reduced the size of the key space.

Britain used machines to guess keys during World War 2. The German Enigma had a very large key space, but the British built specialized computing engines, the Bombes, to mechanically try keys until the day's key was found. This meant that sometimes they found the day's key within hours of the new key's use, but it also meant that on some days they never did find the right key. The Bombes were not general-purpose computers but were precursors to modern-day computers.

Today, computers can guess keys very quickly, and this is why key size is important in modern cryptosystems. The cipher DES uses a 56-bit key, which means that there are 256 possible keys. 256 is 72,057,594,037,927,936 keys. This is a lot of keys, but a general-purpose computer can check the entire key space in a matter of days. A specialized computer can check it in hours. On the other hand, more recently designed ciphers such as 3DES, Blowfish, and IDEA all use 128-bit keys, which means there are 2128 possible keys. This is many, many more keys, and even if all the computers on the planet cooperated, it could still take more time than the age of the universe to find the key.

Public-key ciphers

The primary problem with symmetric ciphers is not their security but with key exchange. Once the sender and receiver have exchanged keys, that key can be used to securely communicate, but what secure communication channel was used to communicate the key itself? In particular, it would probably be much easier for an attacker to work to intercept the key than it is to try all the keys in the key space. Another problem is the number of keys needed. If there are n people who need to communicate, then n(n-1)/2 keys are needed for each pair of people to communicate privately. This may be OK for a small number of people but quickly becomes unwieldy for large groups of people.

Public-key ciphers were invented to avoid the key-exchange problem entirely. A public-key cipher uses a pair of keys for sending messages. The two keys belong to the person receiving the message. One key is a public key and may be given to anybody. The other key is a private key and is kept secret by the owner. A sender encrypts a message using the public key and once encrypted, only the private key may be used to decrypt it.

This protocol solves the key-exchange problem inherent with symmetric ciphers. There is no need for the sender and receiver to agree upon a key. All that is required is that some time before secret communication the sender gets a copy of the receiver's public key. Furthermore, the one public key can be used by anybody wishing to communicate with the receiver. So only n keypairs are needed for n people to communicate secretly with one another.

Public-key ciphers are based on one-way trapdoor functions. A one-way function is a function that is easy to compute, but the inverse is hard to compute. For example, it is easy to multiply two prime numbers together to get a composite, but it is difficult to factor a composite into its prime components. A one-way trapdoor function is similar, but it has a trapdoor. That is, if some piece of information is known, it becomes easy to compute the inverse. For example, if you have a number made of two prime factors, then knowing one of the factors makes it easy to compute the second. Given a public-key cipher based on prime factorization, the public key contains a composite number made from two large prime factors, and the encryption algorithm uses that composite to encrypt the message. The algorithm to decrypt the message requires knowing the prime factors, so decryption is easy if you have the private key containing one of the factors but extremely difficult if you do not have it.

As with good symmetric ciphers, with a good public-key cipher all of the security rests with the key. Therefore, key size is a measure of the system's security, but one cannot compare the size of a symmetric cipher key and a public-key cipher key as a measure of their relative security. In a brute-force attack on a symmetric cipher with a key size of 80 bits, the attacker must enumerate up to 280 keys to find the right key. In a brute-force attack on a public-key cipher with a key size of 512 bits, the attacker must factor a composite number encoded in 512 bits (up to 155 decimal digits). The workload for the attacker is fundamentally different depending on the cipher he is attacking. While 128 bits is sufficient for symmetric ciphers, given today's factoring technology public keys with 1024 bits are recommended for most purposes.

Hybrid ciphers

Public-key ciphers are no panacea. Many symmetric ciphers are stronger from a security standpoint, and public-key encryption and decryption are more expensive than the corresponding operations in symmetric systems. Public-key ciphers are nevertheless an effective tool for distributing symmetric cipher keys, and that is how they are used in hybrid cipher systems.

A hybrid cipher uses both a symmetric cipher and a public-key cipher. It works by using a public-key cipher to share a key for the symmetric cipher. The actual message being sent is then encrypted using the key and sent to the recipient. Since symmetric key sharing is secure, the symmetric key used is different for each message sent. Hence it is sometimes called a session key.

Both PGP and GnuPG use hybrid ciphers. The session key, encrypted using the public-key cipher, and the message being sent, encrypted with the symmetric cipher, are automatically combined in one package. The recipient uses his private-key to decrypt the session key and the session key is then used to decrypt the message.

A hybrid cipher is no stronger than the public-key cipher or symmetric cipher it uses, whichever is weaker. In PGP and GnuPG, the public-key cipher is probably the weaker of the pair. Fortunately, however, if an attacker could decrypt a session key it would only be useful for reading the one message encrypted with that session key. The attacker would have to start over and decrypt another session key in order to read any other message.

Digital signatures

A hash function is a many-to-one function that maps its input to a value in a finite set. Typically this set is a range of natural numbers. A simple hash function is f(x) = 0 for all integers x. A more interesting hash function is f(x) = x mod 37, which maps x to the remainder of dividing x by 37.

A document's digital signature is the result of applying a hash function to the document. To be useful, however, the hash function needs to satisfy two important properties. First, it should be hard to find two documents that hash to the same value. Second, given a hash value it should be hard to recover the document that produced that value.

Some public-key ciphers[3] could be used to sign documents. The signer encrypts the document with his private key. Anybody wishing to check the signature and see the document simply uses the signer's public key to decrypt the document. This algorithm does satisfy the two properties needed from a good hash function, but in practice, this algorithm is too slow to be useful.

An alternative is to use hash functions designed to satisfy these two important properties. SHA and MD5 are examples of such algorithms. Using such an algorithm, a document is signed by hashing it, and the hash value is the signature. Another person can check the signature by also hashing their copy of the document and comparing the hash value they get with the hash value of the original document. If they match, it is almost certain that the documents are identical.

Of course, the problem now is using a hash function for digital signatures without permitting an attacker to interfere with signature checking. If the document and signature are sent unencrypted, an attacker could modify the document and generate a corresponding signature without the recipient's knowledge. If only the document is encrypted, an attacker could tamper with the signature and cause a signature check to fail. A third option is to use a hybrid public-key encryption to encrypt both the signature and document. The signer uses his private key, and anybody can use his public key to check the signature and document. This sounds good but is actually nonsense. If this algorithm truly secured the document it would also secure it from tampering and there would be no need for the signature. The more serious problem, however, is that this does not protect either the signature or document from tampering. With this algorithm, only the session key for the symmetric cipher is encrypted using the signer's private key. Anybody can use the public key to recover the session key. Therefore, it is straightforward for an attacker to recover the session key and use it to encrypt substitute documents and signatures to send to others in the sender's name.

An algorithm that does work is to use a public key algorithm to encrypt only the signature. In particular, the hash value is encrypted using the signer's private key, and anybody can check the signature using the public key. The signed document can be sent using any other encryption algorithm including none if it is a public document. If the document is modified the signature check will fail, but this is precisely what the signature check is supposed to catch. The Digital Signature Standard (DSA) is a public key signature algorithm that works as just described. DSA is the primary signing algorithm used in GnuPG.

Chapter 3. Key Management

Key tampering is a major security weakness with public-key cryptography. An eavesdropper may tamper with a user's keyrings or forge a user's public key and post it for others to download and use. For example, suppose Chloe wants to monitor the messages that Alice sends to Blake. She could mount what is called a man in the middle attack. In this attack, Chloe creates a new public/private keypair. She replaces Alice's copy of Blake's public key with the new public key. She then intercepts the messages that Alice sends to Blake. For each intercept, she decrypts it using the new private key, reencrypts it using Blake's true public key, and forwards the reencrypted message to Blake. All messages sent from Alice to Blake can now be read by Chloe.

Good key management is crucial in order to ensure not just the integrity of your keyrings but the integrity of other users' keyrings as well. The core of key management in GnuPG is the notion of signing keys. Key signing has two main purposes: it permits you to detect tampering on your keyring, and it allows you to certify that a key truly belongs to the person named by a user ID on the key. Key signatures are also used in a scheme known as the web of trust to extend certification to keys not directly signed by you but signed by others you trust. Responsible users who practice good key management can defeat key tampering as a practical attack on secure communication with GnuPG.

Managing your own keypair

A keypair has a public key and a private key. A public key consists of the public portion of the master signing key, the public portions of the subordinate signing and encryption subkeys, and a set of user IDs used to associate the public key with a real person. Each piece has data about itself. For a key, this data includes its ID, when it was created, when it will expire, etc. For a user ID, this data includes the name of the real person it identifies, an optional comment, and an email address. The structure of the private key is similar, except that it contains only the private portions of the keys, and there is no user ID information.

The command-line option --edit-key may be used to view a keypair. For example,

chloe% gpg --edit-key chloe@cyb.org
Secret key is available.

pub  1024D/26B6AAE1  created: 1999-06-15 expires: never      trust: -/u
sub  2048g/0CF8CB7A  created: 1999-06-15 expires: never
sub  1792G/08224617  created: 1999-06-15 expires: 2002-06-14
sub   960D/B1F423E7  created: 1999-06-15 expires: 2002-06-14
(1)  Chloe (Jester) <chloe@cyb.org>
(2)  Chloe (Plebian) <chloe@tel.net>
Command>
The public key is displayed along with an indication of whether or not the private key is available. Information about each component of the public key is then listed. The first column indicates the type of the key. The keyword pub identifies the public master signing key, and the keyword sub identifies a public subordinate key. The second column indicates the key's bit length, type, and ID. The type is D for a DSA key, g for an encryption-only ElGamal key, and G for an ElGamal key that may be used for both encryption and signing. The creation date and expiration date are given in columns three and four. The user IDs are listed following the keys.
More information about the key can be obtained with interactive commands. The command toggle switches between the public and private components of a keypair if indeed both components are available.

Command> toggle

sec  1024D/26B6AAE1  created: 1999-06-15 expires: never
sbb  2048g/0CF8CB7A  created: 1999-06-15 expires: never
sbb  1792G/08224617  created: 1999-06-15 expires: 2002-06-14
sbb   960D/B1F423E7  created: 1999-06-15 expires: 2002-06-14
(1)  Chloe (Jester) <chloe@cyb.org>
(2)  Chloe (Plebian) <chloe@tel.net>
The information provided is similar to the listing for the public-key component. The keyword sec identifies the private master signing key, and the keyword sbb identifies the private subordinates keys. The user IDs from the public key are also listed for convenience.
Key integrity

When you distribute your public key, you are distributing the public components of your master and subordinate keys as well as the user IDs. Distributing this material alone, however, is a security risk since it is possible for an attacker to tamper with the key. The public key can be modified by adding or substituting keys, or by adding or changing user IDs. By tampering with a user ID, the attacker could change the user ID's email address to have email redirected to himself. By changing one of the encryption keys, the attacker would also be able to decrypt the messages redirected to him.

Using digital signatures is a solution to this problem. When data is signed by a private key, the corresponding public key is bound to the signed data. In other words, only the corresponding public key can be used to verify the signature and ensure that the data has not been modified. A public key can be protected from tampering by using its corresponding private master key to sign the public key components and user IDs, thus binding the components to the public master key. Signing public key components with the corresponding private master signing key is called self-signing, and a public key that has self-signed user IDs bound to it is called a certificate.

As an example, Chloe has two user IDs and three subkeys. The signatures on the user IDs can be checked with the command check from the key edit menu.

chloe% gpg --edit-key chloe
Secret key is available.

pub  1024D/26B6AAE1  created: 1999-06-15 expires: never      trust: -/u
sub  2048g/0CF8CB7A  created: 1999-06-15 expires: never
sub  1792G/08224617  created: 1999-06-15 expires: 2002-06-14
sub   960D/B1F423E7  created: 1999-06-15 expires: 2002-06-14
(1)  Chloe (Jester) <chloe@cyb.org>
(2)  Chloe (Plebian) <chloe@tel.net>

Command> check
uid  Chloe (Jester) <chloe@cyb.org>
sig!	   26B6AAE1 1999-06-15	 [self-signature]
uid  Chloe (Plebian) <chloe@tel.net>
sig!	   26B6AAE1 1999-06-15	 [self-signature]
As expected, the signing key for each signature is the master signing key with key ID 0x26B6AAE1. The self-signatures on the subkeys are present in the public key, but they are not shown by the GnuPG interface.
Adding and deleting key components

Both new subkeys and new user IDs may be added to your keypair after it has been created. A user ID is added using the command adduid. You are prompted for a real name, email address, and comment just as when you create an initial keypair. A subkey is added using the command addkey. The interface is similar to the interface used when creating an initial keypair. The subkey may be a DSA signing key, and encrypt-only ElGamal key, or a sign-and-encrypt ElGamal key. When a subkey or user ID is generated it is self-signed using your master signing key, which is why you must supply your passphrase when the key is generated.

Additional user IDs are useful when you need multiple identities. For example, you may have an identity for your job and an identity for your work as a political activist. Coworkers will know you by your work user ID. Coactivists will know you by your activist user ID. Since those groups of people may not overlap, though, each group may not trust the other user ID. Both user IDs are therefore necessary.

Additional subkeys are also useful. The user IDs associated with your public master key are validated by the people with whom you communicate, and changing the master key therefore requires recertification. This may be difficult and time consuming if you communicate with many people. On the other hand, it is good to periodically change encryption subkeys. If a key is broken, all the data encrypted with that key will be vulnerable. By changing keys, however, only the data encrypted with the one broken key will be revealed.

Subkeys and user IDs may also be deleted. To delete a subkey or user ID you must first select it using the key or uid commands respectively. These commands are toggles. For example, the command key 2 selects the second subkey, and invoking key 2 again deselects it. If no extra argument is given, all subkeys or user IDs are deselected. Once the user IDs to be deleted are selected, the command deluid actually deletes the user IDs from your key. Similarly, the command delkey deletes all selected subkeys from both your public and private keys.

For local keyring management, deleting key components is a good way to trim other people's public keys of unnecessary material. Deleting user IDs and subkeys on your own key, however, is not always wise since it complicates key distribution. By default, when a user imports your updated public key it will be merged with the old copy of your public key on his ring if it exists. The components from both keys are combined in the merge, and this effectively restores any components you deleted. To properly update the key, the user must first delete the old version of your key and then import the new version. This puts an extra burden on the people with whom you communicate. Furthermore, if you send your key to a keyserver, the merge will happen regardless, and anybody who downloads your key from a keyserver will never see your key with components deleted. Consequently, for updating your own key it is better to revoke key components instead of deleting them.

for sending and receiving keys.

pip install --upgrade stripe
Install from source with:

python setup.py install
Requirements

Python 3.6+ (PyPy supported)
Python 2.7 deprecation

The Python Software Foundation (PSF) community announced the end of support of Python 2 on 01 January 2020. Starting with version 6.0.0 Stripe SDK Python packages will no longer support Python 2.7. To continue to get new features and security updates, please make sure to update your Python runtime to Python 3.6+.

The last version of the Stripe SDK that supports Python 2.7 is 5.5.0.

Usage

The library needs to be configured with your account's secret key which is available in your Stripe Dashboard. Set stripe.api_key to its value:

from stripe import StripeClient

client = StripeClient("sk_test_...")

# list customers
customers = client.customers.list()

# print the first customer's email
print(customers.data[0].email)

# retrieve specific Customer
customer = client.customers.retrieve("cus_123456789")

# print that customer's email
print(customer.email)
Handling exceptions

Unsuccessful requests raise exceptions. The class of the exception will reflect the sort of error that occurred. Please see the Api Reference for a description of the error classes you should handle, and for information on how to inspect these errors.

Per-request Configuration

Configure individual requests with the options argument. For example, you can make requests with a specific Stripe Version or as a connected account:

from stripe import StripeClient

client = StripeClient("sk_test_...")

# list customers
client.customers.list(
    options={
        "api_key": "sk_test_...",
        "stripe_account": "acct_...",
        "stripe_version": "2019-02-19",
    }
)

# retrieve single customer
client.customers.retrieve(
    "cus_123456789",
    options={
        "api_key": "sk_test_...",
        "stripe_account": "acct_...",
        "stripe_version": "2019-02-19",
    }
)
Configuring an HTTP Client

You can configure your StripeClient to use urlfetch, requests, pycurl, or urllib2 with the http_client option:

client = StripeClient("sk_test_...", http_client=stripe.UrlFetchClient())
client = StripeClient("sk_test_...", http_client=stripe.RequestsClient())
client = StripeClient("sk_test_...", http_client=stripe.PycurlClient())
client = StripeClient("sk_test_...", http_client=stripe.Urllib2Client())
Without a configured client, by default the library will attempt to load libraries in the order above (i.e. urlfetch is preferred with urllib2 used as a last resort). We usually recommend that people use requests.

Configuring a Proxy

A proxy can be configured with the proxy client option:

client = StripeClient("sk_test_...", proxy="https://user:pass@example.com:1234")
Configuring Automatic Retries

You can enable automatic retries on requests that fail due to a transient problem by configuring the maximum number of retries:

client = StripeClient("sk_test_...", max_network_retries=2)
Various errors can trigger a retry, like a connection error or a timeout, and also certain API responses like HTTP status 409 Conflict.

Idempotency keys are automatically generated and added to requests, when not given, to guarantee that retries are safe.

Logging

The library can be configured to emit logging that will give you better insight into what it's doing. The info logging level is usually most appropriate for production use, but debug is also available for more verbosity.

There are a few options for enabling it:

Set the environment variable STRIPE_LOG to the value debug or info

$ export STRIPE_LOG=debug
Set stripe.log:

import stripe
stripe.log = 'debug'
Enable it through Python's logging module:

import logging
logging.basicConfig()
logging.getLogger('stripe').setLevel(logging.DEBUG)
Accessing response code and headers

You can access the HTTP response code and headers using the last_response property of the returned resource.

customer = client.customers.retrieve(
    "cus_123456789"
)

print(customer.last_response.code)
print(customer.last_response.headers)
Writing a Plugin

If you're writing a plugin that uses the library, we'd appreciate it if you identified using stripe.set_app_info():

stripe.set_app_info("MyAwesomePlugin", version="1.2.34", url="https://myawesomeplugin.info")
This information is passed along when the library makes calls to the Stripe API.

Telemetry

By default, the library sends telemetry to Stripe regarding request latency and feature usage. These numbers help Stripe improve the overall latency of its API for all users, and improve popular features.

You can disable this behavior if you prefer:

stripe.enable_telemetry = False
Types

In v7.1.0 and newer, the library includes type annotations. See the wiki for a detailed guide.

Please note that some annotations use features that were only fairly recently accepted, such as Unpack[TypedDict] that was accepted in January 2023. We have tested that these types are recognized properly by Pyright. Support for Unpack in MyPy is still experimental, but appears to degrade gracefully. Please report an issue if there is anything we can do to improve the types for your type checker of choice.

Types and the Versioning Policy

We release type changes in minor releases. While stripe-python follows semantic versioning, our semantic versions describe the runtime behavior of the library alone. Our type annotations are not reflected in the semantic version. That is, upgrading to a new minor version of stripe-python might result in your type checker producing a type error that it didn't before. You can use a ~=x.x or x.x.* version specifier in your requirements.txt to constrain pip to a certain minor range of stripe-python.

Types and API Versions

The types describe the Stripe API version that was the latest at the time of release. This is the version that your library sends by default. If you are overriding stripe.api_version / stripe_version on the StripeClient, or using a webhook endpoint tied to an older version, be aware that the data you see at runtime may not match the types.

Beta SDKs

Stripe has features in the beta phase that can be accessed via the beta version of this package. We would love for you to try these and share feedback with us before these features reach the stable phase. To install a beta version use pip install with the exact version you'd like to use:

pip install stripe==v8.6.0b1
Note There can be breaking changes between beta versions. Therefore we recommend pinning the package version to a specific beta version in your requirements file or setup.py. This way you can install the same version each time without breaking changes unless you are intentionally looking for the latest beta version.
We highly recommend keeping an eye on when the beta feature you are interested in goes from beta to stable so that you can move from using a beta version of the SDK to the stable version.

If your beta feature requires a Stripe-Version header to be sent, set the stripe.api_version field using the stripe.add_beta_version function:

stripe.add_beta_version("feature_beta", "v3")
Support

New features and bug fixes are released on the latest major version of the Stripe Python library. If you are on an older major version, we recommend that you upgrade to the latest in order to use the new features and bug fixes including those for security vulnerabilities. Older major versions of the package will continue to be available for use, but will not be receiving any updates.

Development

The test suite depends on stripe-mock, so make sure to fetch and run it from a background terminal (stripe-mock's README also contains instructions for installing via Homebrew and other methods):

go install github.com/stripe/stripe-mock@latest
stripe-mock
Run the following command to set up the development virtualenv:

make
Run all tests on all supported Python versions:

make test
Run all tests for a specific Python version (modify -e according to your Python target):

TOX_ARGS="-e py37" make test
Run all tests in a single file:

TOX_ARGS="-e py37 -- tests/api_resources/abstract/test_updateable_api_resource.py" make test
Run a single test suite:

TOX_ARGS="-e py37 -- tests/api_resources/abstract/test_updateable_api_resource.py::TestUpdateableAPIResource" make test
Run a single test:

TOX_ARGS="-e py37 -- tests/api_resources/abstract/test_updateable_api_resource.py::TestUpdateableAPIResource::test_save" make test
Run the linter with:

make lint
The library uses Black for code formatting. Code must be formatted with Black before PRs are submitted, otherwise CI will fail. Run the formatter with:

make fmt

pub   rsa3072 2017-03-17 [SC] [expires: 2027-03-15]
      5B80 C575 4298 F0CB 55D8  ED6A BCEF 7E29 4B09 2E28
uid   Andre Heinecke (Release Signing Key)

pub   ed25519 2020-08-24 [SC] [expires: 2030-06-30]
      6DAA 6E64 A76D 2840 571B  4902 5288 97B8 2640 3ADA
uid   Werner Koch (dist signing 2020)

pub   ed25519 2021-05-19 [SC] [expires: 2027-04-04]
      AC8E 115B F73E 2D8D 47FA  9908 E98E 9B2D 19C6 C8BD
uid   Niibe Yutaka (GnuPG Release Key)

pub   brainpoolP256r1 2021-10-15 [SC] [expires: 2029-12-31]
      02F3 8DFF 731F F97C B039  A1DA 549E 695E 905B A208
uid   GnuPG.com (Release Signing Key 2021)
Below is a public key block with the above keys; you may also download these keys from: https://gnupg.org/signature_key.asc

-----BEGIN PGP PUBLIC KEY BLOCK-----

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
eiInsGZ/+h5XYrpXTgA=
=4+Sn
-----END PGP PUBLIC KEY BLOCK-----
Former Signature Keys
Releases done in the years 2006 to 2010 were signed by this key:

pub   rsa1024/1CE0C630 2006-01-01 [expired: 2011-06-30]
      Key fingerprint = 7B96 D396 E647 1601 754B  E4DB 53B6 20D0 1CE0 C630
uid   [ expired] Werner Koch (dist sig) <dd9jn@gnu.org>
Releases done in the years 1998 to 2005 were signed by this key:

pub   dsa1024/57548DCD 1998-07-07 [expired: 2005-12-31]
      Key fingerprint = 6BD9 050F D8FC 941B 4341  2DCC 68B7 AB89 5754 8DCD
uid   [ expired] Werner Koch (gnupg sig) <dd9jn@gnu.org>
For reference a public key block with expired keys used to sign older releases can be found here.

-----BEGIN PGP PUBLIC KEY BLOCK-----

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
pVsjxpylBcSjsPE8MAki7Hb2Rw==
=syRC
-----END PGP PUBLIC KEY BLOCK-----
