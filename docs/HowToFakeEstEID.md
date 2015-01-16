Target: a) create a minimal, functional smart card that looks identical (in the sense of being identified by OpenSC and qesteidutil) to an Estonian ID-card b) make it do crypto with arbitrary keys and certificates!

# What you need to know and read beforehand
* http://www.id.ee/public/EstEID_Spetsifikatsioon_v2.01.pdf
* http://www.id.ee/public/EstEID_kaardi_kasutusjuhend.pdf
* https://www.opensc-project.org/opensc/attachment/wiki/MICARDO/mic21_druck.pdf
* ISO7816-* (mostly 4). http://www.cardwerk.com/smartcards/smartcard_standard_ISO7816-4.aspx

# Tools used
* One 1st generation Micardo EstEID card belonging to some nice real person and my own ID-card from 2011 for reference.
* Baastarkvara 3.8.1 and https://github.com/martinpaljak/ria-debian-build
* One OmniKey 1021 USB smart card reader
* One Debian Linux & script-fu
* One Oberthur Cosmo v7 JavaCard
* Time and witchcraft

# What is (on) the card?
Do:
* ```sudo touch /tmp/smartcardpp.log```
* and  ```sudo chmod 777 /tmp/smartcardpp.log```
* and run ```qesteidutil```.
* Also run ```pkcs15-tool -D -vvvvv > opensc.log >2&1```.

Then run your grep/sed/awk/cut/sort/uniq-fu on the generates .log files to get the APDU-s sent to the card. This gives you :

    00 A4 00 00 00 
    00 A4 00 08 00  
    00 A4 01 08 02 EE EE 00  
    00 A4 01 0C 02 EE EE 
    00 A4 02 04 02 00 13 00  
    00 A4 02 04 02 00 16 
    00 A4 02 04 02 50 44 
    00 A4 02 04 02 50 44 00  
    00 A4 02 04 02 AA CE 
    00 A4 02 04 02 AA CE 00  
    00 A4 02 04 02 DD CE 
    00 A4 02 04 02 DD CE 00  
    00 A4 02 08 02 00 16 00  
    00 A4 02 08 02 00 33 00  
    00 B0 00 00 00 
    00 B0 00 00 FE  
    00 B0 00 FE FE  
    00 B0 01 00 00 
    00 B0 01 FC FE  
    00 B0 02 00 00 
    00 B0 02 FA FE  
    00 B0 03 00 00 
    00 B0 03 F8 FE  
    00 B0 04 00 00 
    00 B0 04 F6 FE  
    00 B0 05 00 00 
    00 B0 05 F4 0C  
    00 B2 01 04 00  
    00 B2 01 04 80 
    00 B2 02 04 00  
    00 B2 02 04 80 
    00 B2 03 04 00  
    00 B2 03 04 80 
    00 B2 04 04 00  
    00 B2 05 04 00  
    00 B2 06 04 00  
    00 B2 07 04 00  
    00 B2 08 04 00  
    00 B2 08 04 80 
    00 B2 09 04 00  
    00 B2 0A 04 00  
    00 B2 0B 04 00  
    00 B2 0C 04 00  
    00 B2 0D 04 00  
    00 B2 0E 04 00  
    00 B2 0F 04 00  
    00 B2 10 04 00  
    00 C0 00 00 06 
    00 C0 00 00 08 
    00 C0 00 00 0A 
    00 C0 00 00 19 
    00 C0 00 00 1A 
    00 C0 00 00 28 

This boils down to the following INS-tructions, all within standard ISO CLA-ss 0x00:

    A4 - SELECT with the following file identifiers:
         EEEE
         0013
         0016
         0033
         5044
         AACE
         DDCE
    B0 - READ BINARY
    B2 - READ RECORD
    C0 - (GET RESPONSE) - this is handled transparently by JavaCard

Using `opensc-explorer` we poke around and fetch those files on the card and get the content and file control information which we shall handle as magical binary blobs:

    # fci for fid-s: 
    3F00: 6F 26 82 01 38 83 02 3F 00 84 02 4D 46 85 02 57 3E 8A 01 05 A1 03 8B 01 02 81 08 D2 76 00 00 28 FF 05 2D 82 03 03 00 00
    EEEE: 6F 25 82 01 38 83 02 EE EE 84 10 D2 33 00 00 01 00 00 01 00 00 00 00 00 00 00 00 85 02 57 3E 8A 01 05 A1 03 8B 01 02
    DDCE: 62 18 82 01 01 83 02 DD CE 85 02 06 00 8A 01 05 A1 08 8B 06 00 30 03 06 00 01
    AACE: 62 18 82 01 01 83 02 AA CE 85 02 06 00 8A 01 05 A1 08 8B 06 00 30 03 06 00 01
    5044: 62 17 82 05 04 41 00 32 10 83 02 50 44 85 02 01 8C 8A 01 05 A1 03 8B 01 01
    0016: 62 17 82 05 04 41 00 0C 03 83 02 00 16 85 02 00 1A 8A 01 05 A1 03 8B 01 01
    0013: 62 18 82 05 02 41 00 4F 04 83 02 00 13 8A 01 05 A1 08 8B 06 00 30 03 07 00 01
    0033: 62 18 82 05 02 41 00 15 01 83 02 00 33 8A 01 05 A1 08 8B 06 00 30 03 07 00 01
    
    # records in files
    # eeee/0013:
    83 04 01 00 10 01 C0 02 81 80 91 03 FF FF F8 7B 18 80 01 00 A1 0A 8B 08 00 30 01 03 02 04 03 05 B6 07 95 01 40 89 02 13 10 7B 11 80 01 06 A1 03 8B 01 09 B8 07 95 01 40 89 02 11 30 7B 11 80 01 07 A1 03 8B 01 0A B8 07 95 01 40 89 02 11 30
    83 04 02 00 10 02 C0 02 81 80 91 03 FF FF FF 7B 18 80 01 00 A1 0A 8B 08 00 30 01 03 02 04 03 05 F6 07 95 01 40 89 02 13 10 7B 11 80 01 06 A1 03 8B 01 09 B8 07 95 01 40 89 02 11 30 7B 11 80 01 07 A1 03 8B 01 0A B8 07 95 01 40 89 02 11 30
    83 04 11 00 10 11 C0 02 81 80 91 03 FF FF DA 7B 18 80 01 00 A1 0A 8B 08 00 30 01 03 02 04 03 05 A4 07 95 01 40 89 02 21 13 7B 11 80 01 06 A1 03 8B 01 0B B8 07 95 01 40 89 02 11 30 7B 11 80 01 07 A1 03 8B 01 0C B8 07 95 01 40 89 02 11 30
    83 04 12 00 10 12 C0 02 81 80 91 03 FF FF FF 7B 18 80 01 00 A1 0A 8B 08 00 30 01 03 02 04 03 05 E4 07 95 01 40 89 02 21 13 7B 11 80 01 06 A1 03 8B 01 0B B8 07 95 01 40 89 02 11 30 7B 11 80 01 07 A1 03 8B 01 0C B8 07 95 01 40 89 02 11 30

    # eeee/0033:
    00 A4 08 95 01 40 83 03 80 11 00 B6 08 95 01 40 83 03 80 01 00

    # 0016:
    80 01 03 90 01 03 83 02 00 00
    80 01 03 90 01 03 83 02 00 00
    80 01 03 90 01 03

Emulating these files and implementing the three basic filesystem commands (SELECT, READ BINARY, READ RECORD) already gives enough to be "identified" as EstEID:

    $ eidenv -w
    Waiting for a card to be inserted...
    Surname: AAAAAAAAAAAAAAAAAAAAAAAAAAAA
    Given names 1: AAAAAAAAAAAAAAA
    Given names 2: AAAAAAAAAAAAAAA
    Sex: A
    Citizenship: AAA
    Date of birth: AAAAAAAAAA
    Personal ID code: AAAAAAAAAAA
    Document number: AAAAAAAA
    Expiry date: AAAAAAAAAA
    Place of birth: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
    Issuing date: AAAAAAAAAA
    Permit type: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
    Remark 1: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
    Remark 2: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
    Remark 3: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
    Remark 4: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA

or try `pkcs15-tool -D`. Or `qesteidutil`:

![qesteidutil initial screenshot](http://i.imgur.com/ASFWoGP.png)

# PIN commands
We don't really care about PIN verification, thus all commands trying to verify a PIN code return success (0x9000).
But it makes sense to store the PIN code, for future reference. In some future release, tuneable fuzzing shall be possible.

# Crypto commands
Repeat the above APDU sniffing methods and do things with the card - browser, digidoc sign and encrypt etc. You get these additional commands:

    22 - MANAGE SECURITY ENVIRONMENT - like with PINs - ignored, as the state is implicitly known
    88 - INTERNAL AUTHENTICATE - RSA signature with authentication key
    2A - PERFORM SECURITY OPERATION with variants
       - 9E9A RSA signature with signature key
       - 8086 and decryption with authentication key

# Set/get values
For this we shall use a class byte from the "proprietary" range: 0x80. The following fields are readable-settable:
    
    0x01 - set ATR historical bytes
    0x02 - load certificates
    0x03 - get/set RSA key components
    0x04 - get/set personal data file records
    0x05 - get/set PIN values

# Necessary changes in [Baastarkvara](https://github.com/martinpaljak/idkaart_public)
* https://github.com/OpenSC/OpenSC/pull/214
* https://github.com/martinpaljak/idkaart_public/commit/34ca5baa5ede2d70649770d6371def5c82187879

# Howto build-install-edit

    # Build the FakeEstEIDApplet in AppletPlayground project and (re)install to the card
    ant e && gp -install FakeEstEIDApplet.cap -reinstall -default -d -v
    # Build the esteidhacker project and generate a SK certificates look-alike
    # If not using OpenJDK you need to install the "unlimited crypto" thing from http://www.oracle.com/technetwork/java/javase/downloads/jce-7-download-432124.html
    ant && java -jar esteid.jar -genca fake.ca
    # populate data to the card. Add -check for cryptographic consistency checks
    java -jar esteid.jar -ca fake.ca -new

# End result
* Applet (MIT): https://github.com/martinpaljak/AppletPlayground/blob/master/src/pro/javacard/applets/FakeEstEIDApplet.java
* CA-imitation (GPL): https://github.com/martinpaljak/esteidhacker/blob/master/src/esteidhacker/FakeEstEIDCA.java
* Helping hand utility (GPL): https://github.com/martinpaljak/esteidhacker/blob/master/src/esteidhacker/FakeEstEID.java

#### Card in qesteidutil (with oneliner patch)
![qesteidutil screenshot](http://martinpaljak.net/fakecard.png)

#### Card in qdigidoc, after a successful signing operation
![qdigidoc screenshot](http://martinpaljak.net/fakesignature.png)
    