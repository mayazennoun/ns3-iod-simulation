# Scenarios d'attaque — Avec blockchain

Ce dossier contient cinq simulations NS-3 d'attaques testees sur le systeme avec le mecanisme de securite complet actif : blockchain delay 100ms modelise et DropTail 100 paquets sur l'Edge.

---

## Vue d ensemble

| Attaque | Fichier | Resultat | Impact legitimes |
|---|---|---|---|
| Eavesdropping | iod-attack-eavesdrop.cc | Totalement bloquee | Aucun |
| Replay | iod-attack-replay.cc | Totalement bloquee | Aucun |
| Spoofing | iod-attack-spoofing.cc | Totalement bloquee | Minimal |
| DoS UDP Flood | iod-attack-dos.cc | Partiellement attenueee | Modere |
| DDoS UDP Flood | iod-attack-ddos.cc | Partiellement attenueee | Severe |

---

## Eavesdropping

Un attaquant ecoute passivement le canal Wi-Fi via MonitorSnifferRx. Il capture 85% des trames mais ne peut dechiffrer aucun octet car ChaCha20-Poly1305 protege le contenu avec une cle K_DE inconnue de l'attaquant.

```bash
cp iod-attack-eavesdrop.cc ~/ns-allinone-3.43/ns-3.43/scratch/
./ns3 build scratch/iod-attack-eavesdrop
./ns3 run scratch/iod-attack-eavesdrop
```

**Resultats** : 374 101 paquets captures / 0 dechiffrables / impact legitimes 0%

---

## Replay

L'attaquant capture des paquets pendant 30s puis les rejoue vers l'Edge. Chaque paquet est rejete car ChaCha20-Poly1305 utilise un nonce unique et incrementiel par trame — tout nonce deja vu est refuse.

```bash
cp iod-attack-replay.cc ~/ns-allinone-3.43/ns-3.43/scratch/
./ns3 build scratch/iod-attack-replay
./ns3 run scratch/iod-attack-replay
```

**Resultats** : 12 500 paquets rejoues / 0 acceptes / impact legitimes 0%

---

## Spoofing

Un faux drone tente de se connecter et de streamer vers l'Edge. L'Edge appelle isAuthorized(ID_FAKE) sur le smart contract blockchain qui retourne false. Tous les paquets sont rejetes.

```bash
cp iod-attack-spoofing.cc ~/ns-allinone-3.43/ns-3.43/scratch/
./ns3 build scratch/iod-attack-spoofing
./ns3 run scratch/iod-attack-spoofing
```

**Resultats** : 42 364 paquets envoyes / 42 364 rejetes (100%) / 0 Mbps recu par l'Edge

---

## DoS UDP Flood

Un attaquant envoie un flood UDP a 100 Mbps vers l'Edge a partir de t=10s. Le DropTail 100 paquets filtre une partie du trafic (103 paquets droppes). Le debit legitime chute de 8.00 a 6.94 Mbps.

```bash
cp iod-attack-dos.cc ~/ns-allinone-3.43/ns-3.43/scratch/
./ns3 build scratch/iod-attack-dos
./ns3 run scratch/iod-attack-dos
# Varier le taux :
./ns3 run "scratch/iod-attack-dos --dosRate=50Mbps"
./ns3 run "scratch/iod-attack-dos --dosRate=200Mbps"
```

**Resultats** :
- Paquets flood envoyes : 378 406
- Debit DoS recu Edge : 53.89 Mbps
- Paquets droppes queue : 103
- Debit legitime : 6.94 Mbps (contre 8.00)
- Perte legitime : 12.34%
- Latence legitime : 103 ms

---

## DDoS UDP Flood

Un botnet de 3 attaquants envoie chacun 50 Mbps vers l'Edge (150 Mbps total) a partir de t=10s. L'impact est significativement plus severe que le DoS simple malgre le DropTail qui droppe 178 paquets.

```bash
cp iod-attack-ddos.cc ~/ns-allinone-3.43/ns-3.43/scratch/
./ns3 build scratch/iod-attack-ddos
./ns3 run scratch/iod-attack-ddos
```

**Resultats** :
- Paquets flood envoyes : 391 676
- Debit DDoS recu Edge : 45.06 Mbps
- Paquets droppes queue : 178
- Perte moyenne attaquants : 67.21%
- Debit legitime : 5.03 Mbps (contre 8.00)
- Perte legitime : 34.55%
- Latence legitime : 157 ms

---

## Comparaison DoS vs DDoS

| Metrique | DoS (1 attaquant) | DDoS (3 attaquants) |
|---|---|---|
| Debit flood total | 100 Mbps | 150 Mbps |
| Debit recu Edge | 53.89 Mbps | 45.06 Mbps |
| Paquets droppes | 103 | 178 |
| Debit legitime | 6.94 Mbps | 5.03 Mbps |
| Perte legitime | 12.34% | 34.55% |
| Impact | Modere | Severe |

---

## Conclusion

Les trois attaques cryptographiques et d'identite sont neutralisees completement. Les attaques volumetriques (DoS et DDoS) sont partiellement attenuees — c'est une limite connue des architectures Wi-Fi a canal partage. Un filtrage applicatif par adresse source sur l'Edge constitue une amelioration directe.
