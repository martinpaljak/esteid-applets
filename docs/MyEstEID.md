## v0.1 specification
In spirit and setting very much similar to [OpenPGP 2.0](http://g10code.com/docs/openpgp-card-2.0.pdf). Re-uses [[FakeEstEID]] for giving an [EstEID](http://esteid.org/) compatible interface but unlike FakeEstEID is actually intended for securely generating and storing keys. Suitable for personal use or for small rollouts (a small company with a single security officer issuing cards)

* 3 (4) PIN codes
 * PIN1 - authentication & decryption
 * PIN2 - signature
 * PUK (Admin PIN)
 * Duress PIN (erases all keys and data if used)
* 3 key and certificate pairs
 * Currently only RSA2048 due to Baastarkvara limitations
 * Authentication (PIN1)
 * Digital signature (PIN2 every time)
 * Encryption (PIN1)
* Ability to re-generate keys
 * Requires PUK code
 * Overwriting existing key requires relevant PIN code
* Ability to import keys
 * Requires PUK code
 * Overwriting existing key requires relevant PIN code
* Ability to write Personal Data file
 * Requires PUK code
* Ability to write special options
 * Requires PUK code
 * Duress PIN on/off
 * Contactless interface on/off
 * ... 

## Command line utility
* Added to [esteidhacker](https://github.com/martinpaljak/esteidhacker#esteid-hacker)

----
All about the [EstEID](http://esteid.org)
