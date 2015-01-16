* See https://github.com/martinpaljak/AppletPlayground/wiki/HowToFakeEstEID for implementation notes.

## What it is ?
 * *Looks like a real EstEID card to the extent possible** so that it makes existing software (OpenSC, [Baastarkvara](https://github.com/martinpaljak/idkaart_public)) use/see the keys and personal data file
 * All meaningful data on the card can be set to arbitrary values (within the size limits of the specification). No effort is made to enforce correctness of data formats.
 * All non-interesting data is static. All checks and restrictions (PIN etc) are optional and can be turned off or on by the cardholder.
 * Unlike a real card, cryptographic primitives can be mixed up at will, no checks of private keys vs certificates is made.
 * Unlike a real card, cryptographic material can be imported and exported as wanted. Actual key material doesn't match real cards, obviously!
 * Unlike a real card, no defensive measures are implemented, only a "positive path" imitation.
 * In other words: a truly *read-write* card!

\* Except for the ATR, which requires special hardware to emulate or special arrangements with smart card vendor(s). ATR *historical bytes* **can** be changed where supported by underlying JavaCard/hardware platform.  **NOTE**: Dependance on such *"hardware constants"* like card UID-s (compare: Yhiskaart and chinese Mifares) or ATR-s (compare: MAC filtering and every other ethernet card that supports changing your MAC) is dumb, but unfortunately understandable. Microsoft should fix their minidriver infrastructure for example.

## "Specification" for v0.1
Interesting data (read-write):
 * Authentication key
 * Authentication certificate
 * Signature key
 * Signature certificate
 * Personal data file as specified in original specification
 * PIN1, PIN2 and PUK (all NOP)

Less interesting data (read-only):
 * File system imitation for files used by Baastarkvara

Helper utility:
 * Command line utility to set the values on the card to wanted values
 * Certificate cloning/imitation facility to make certificates that look like the ones issued by [SK](http://sk.ee/)

## Purposes
* Educational and testing
 * Fuzzing (v0.2)
* Hardware key container for PKCS#11 software
 * "No-extra drivers" but "my own keys and certificates"

## Links
* https://github.com/OpenSC/OpenSC/pull/214
* [zee Baastarkvara on Github](https://github.com/martinpaljak/idkaart_public#github-mirror-of-zee-baastarkvara)
