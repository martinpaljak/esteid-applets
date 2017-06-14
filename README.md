# EstEID compatible JavaCard applets
Various JavaCard applets compatible<sup>*</sup> to EstEID chip protocol:

* [FakeEstEID](./docs/FakeEstEID.md)
* [MyEstEID](./docs/MyEstEID.md)
 
\* compatibility here only means that existing host-side software (Baastarkvara, OpenSC etc) can use the important parts of the card: keys and certificates with PIN verification. No "formal (full) compatibility" is implied.

Use:

```
git clone --recursive https://github.com/martinpaljak/esteid-applets
make -C esteid-applets
```

## Similar and related projects
* https://code.google.com/p/eid-quick-key-toolset/
