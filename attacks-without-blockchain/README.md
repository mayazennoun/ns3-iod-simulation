# Attaques sans blockchain

Ce dossier contient quatre simulations NS-3 des memes attaques mais sans le mecanisme de securite blockchain. L'objectif est de comparer les resultats avec et sans blockchain pour demontrer l'apport concret de la blockchain dans le systeme.

---

## Differences avec le dossier attack-scenarios/

| Element | Avec blockchain | Sans blockchain |
|---|---|---|
| Blockchain delay | 100 ms au demarrage | Absent |
| Verification identite | isAuthorized via smart contract | Absente |
| Journal d audit | Enregistrement sur chaine | Absent |
| DropTail | 100 paquets sur Edge | Absent |
| Token de session | Signe et lie a un nonce | Absent |

---

## Vue d ensemble

| Attaque | Fichier | Resultat sans blockchain |
|---|---|---|
| Eavesdropping | iod-attack-eavesdrop-nobc.cc | Chiffrement tient — identite non verifiee |
| Replay | iod-attack-replay-nobc.cc | Vulnerable — pas de verification nonce |
| Spoofing | iod-attack-spoofing-nobc.cc | **Reussie — faux drone accepte** |
| DoS UDP Flood | iod-attack-dos-nobc.cc | Non attenueee — impact plus severe |

---

## Eavesdropping sans blockchain

L'attaquant capture 85% des trames comme avec blockchain. Le chiffrement ChaCha20 reste actif dans les deux cas donc 0 paquet est dechiffrable. La difference est que sans blockchain, n'importe qui peut se connecter librement a l'Edge sans verification d'identite et sans laisser de trace dans un journal d audit.

```bash
cp iod-attack-eavesdrop-nobc.cc ~/ns-allinone-3.43/ns-3.43/scratch/
./ns3 build scratch/iod-attack-eavesdrop-nobc
./ns3 run scratch/iod-attack-eavesdrop-nobc
```

**Resultats** :
- Paquets captures : 374 977
- Paquets dechiffrables : 0
- L_total sans L_bc : 24.6 ms (contre 118 ms avec blockchain)
- Verification identite : absente
- Journal audit : absent

---

## Replay sans blockchain

Sans blockchain il n'y a pas de token de session signe ni de verification de nonce. Les paquets rejoues pourraient potentiellement etre acceptes par l'Edge car aucun mecanisme ne detecte la reutilisation de nonces au niveau applicatif.

```bash
cp iod-attack-replay-nobc.cc ~/ns-allinone-3.43/ns-3.43/scratch/
./ns3 build scratch/iod-attack-replay-nobc
./ns3 run scratch/iod-attack-replay-nobc
```

**Resultats** :
- Paquets rejoues : 12 500
- L_total sans L_bc : 24.8 ms
- Verification nonce : absente
- Resultat : vulnerable

---

## Spoofing sans blockchain

C'est la demonstration la plus frappante. Sans blockchain, le faux drone se connecte librement au reseau Wi-Fi, obtient une session sans aucune verification et injecte un flux video non autorise a plein debit. L'Edge accepte tout car il n y a aucun appel isAuthorized.

```bash
cp iod-attack-spoofing-nobc.cc ~/ns-allinone-3.43/ns-3.43/scratch/
./ns3 build scratch/iod-attack-spoofing-nobc
./ns3 run scratch/iod-attack-spoofing-nobc
```

**Resultats** :
- Paquets envoyes par le faux drone : 63 525
- Debit recu par l Edge : **7.91 Mbps**
- Perte cote faux drone : 0%
- L_total sans L_bc : 39 ms
- Resultat : **ATTAQUE REUSSIE**

---

## DoS sans blockchain

Sans blockchain et sans DropTail, l'attaquant flood l'Edge sans aucun filtrage. L'impact est plus severe qu avec blockchain.

```bash
cp iod-attack-dos-nobc.cc ~/ns-allinone-3.43/ns-3.43/scratch/
./ns3 build scratch/iod-attack-dos-nobc
./ns3 run scratch/iod-attack-dos-nobc
```

**Resultats** :
- Paquets flood envoyes : 383 047
- Debit legitime : 6.85 Mbps (contre 6.94 avec blockchain)
- Perte legitime : 13.48% (contre 12.34% avec blockchain)
- Latence legitime : 111 ms (contre 103 ms avec blockchain)
- Resultat : non attenueee

---

## Tableau comparatif global

| Attaque | Avec blockchain | Sans blockchain |
|---|---|---|
| Eavesdropping | Bloquee — 0 dechiffrable | Chiffrement tient — identite non verifiee |
| Replay | Bloquee — nonce verifie | Vulnerable |
| Spoofing | Bloquee — 100% rejetes | **Reussie — 7.91 Mbps acceptes** |
| DoS | Partiellement attenueee (12.34% perte) | Non attenueee (13.48% perte) |

---

## Conclusion

La blockchain apporte une protection decisive contre le spoofing — la seule attaque qui reussit completement en son absence. Pour l'eavesdropping, le chiffrement ChaCha20 reste la defense principale independamment de la blockchain. Pour le replay, la blockchain renforce la securite via la verification du token de session. Pour le DoS, la blockchain contribue via le DropTail mais l'impact reste present dans les deux cas.
