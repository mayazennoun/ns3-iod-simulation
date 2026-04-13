# Scenarios d'attaque — Simulations NS-3

Ce dossier contient quatre scripts de simulation NS-3, chacun ciblant un scenario d'attaque specifique mentionne dans les exigences du projet. L'objectif n'est pas de demontrer des failles mais de montrer comment le cadre propose resiste a des menaces realistes.

Chaque simulation est independante. Les resultats s'affichent dans le terminal a la fin de chaque execution.

---

## Ce qui est teste

Le cadre combine le chiffrement ChaCha20-Poly1305, la verification d'identite par blockchain, et le filtrage au niveau de l'Edge. Les quatre attaques ci-dessous ont ete choisies car elles representent les menaces les plus courantes dans les reseaux de drones sans fil.

---

## Fichiers

### iod-attack-eavesdrop.cc

Un noeud attaquant est place au centre de la zone de couverture Wi-Fi. Il ecoute passivement le canal 802.11ac partage via l'interface MonitorSnifferRx, capturant tout ce qui passe dans l'air.

Le resultat : l'attaquant peut capturer une grande partie des trames (environ 85% dans nos tests) mais lit exactement zero octet de contenu video. ChaCha20-Poly1305 avec un nonce unique par trame rend chaque paquet capture inutilisable sans la cle de session K_DE, qui ne circule jamais en clair.

Le flux video legitime n'est pas affecte — l'ecoute passive n'introduit aucune interference.

**Pour lancer :**
```bash
cp iod-attack-eavesdrop.cc ~/ns-allinone-3.43/ns-3.43/scratch/
cd ~/ns-allinone-3.43/ns-3.43
./ns3 build scratch/iod-attack-eavesdrop
./ns3 run scratch/iod-attack-eavesdrop
```

**Resultats obtenus :**
- Paquets captures par l'attaquant : 374 101 sur 440 267 emis
- Paquets dechiffrables : 0
- Impact sur le debit legitime : 0%

---

### iod-attack-replay.cc

L'attaquant passe les 30 premieres secondes de la simulation a capturer des paquets. A t=30s, il commence a les rejouer vers l'Edge a 4 Mbps, tentant d'usurper une session drone legitime.

Chaque paquet rejoue est rejete. ChaCha20-Poly1305 est un schema AEAD — chaque trame est chiffree avec un nonce unique et incrementiel. Tout paquet arrivant avec un nonce deja vu echoue immediatement a la verification d'authentification. Il n'existe aucun moyen de rejouer une trame valide.

**Pour lancer :**
```bash
cp iod-attack-replay.cc ~/ns-allinone-3.43/ns-3.43/scratch/
cd ~/ns-allinone-3.43/ns-3.43
./ns3 build scratch/iod-attack-replay
./ns3 run scratch/iod-attack-replay
```

**Resultats obtenus :**
- Paquets rejoues : 12 500
- Paquets acceptes par l'Edge : 0
- Impact sur le debit legitime : 0%

---

### iod-attack-spoofing.cc

Un faux noeud drone se connecte au meme reseau Wi-Fi et tente de streamer de la video vers l'Edge comme s'il etait un dispositif enregistre. Il emet a 8 Mbps en continu.

L'Edge interroge le smart contract blockchain : isAuthorized(ID_FAKE) retourne false. Aucune cle de session n'est delivree, aucun token n'est genere, et la totalite des paquets du faux drone est rejetee. Le taux de rejet est de 100%.

Les drones legitimes subissent un leger impact — environ 4% de perte de paquets — car le trafic du faux drone compete pour le temps d'acces sur le canal Wi-Fi partage. C'est un comportement attendu qui reflete un scenario reel ou un emetteur non autorise cree de la congestion radio.

**Pour lancer :**
```bash
cp iod-attack-spoofing.cc ~/ns-allinone-3.43/ns-3.43/scratch/
cd ~/ns-allinone-3.43/ns-3.43
./ns3 build scratch/iod-attack-spoofing
./ns3 run scratch/iod-attack-spoofing
```

**Resultats obtenus :**
- Paquets envoyes par le faux drone : 42 364
- Paquets rejetes : 42 364 (100%)
- Debit recu par l'Edge depuis le faux drone : 0 Mbps
- Debit legitime : 7.65 Mbps (legere degradation par congestion radio)

---

### iod-attack-dos.cc

LOADING

**Pour lancer :**
```bash
cp iod-attack-dos.cc ~/ns-allinone-3.43/ns-3.43/scratch/
cd ~/ns-allinone-3.43/ns-3.43
./ns3 build scratch/iod-attack-dos
./ns3 run scratch/iod-attack-dos
```

On peut aussi varier le taux de flood :
```bash
./ns3 run "scratch/iod-attack-dos --dosRate=50Mbps"
./ns3 run "scratch/iod-attack-dos --dosRate=200Mbps"
```

**Resultats obtenus :**
LOADING

---

## Recapitulatif

| Attaque | Mecanisme de defense | Resultat | Impact legitimes |
|---|---|---|---|
| Eavesdropping | Chiffrement ChaCha20-Poly1305 | Totalement bloquee | Aucun |
| Replay | Nonce unique par trame (AEAD) | Totalement bloquee | Aucun |
| Spoofing | Verification identite blockchain | Totalement bloquee | Minimal |
| DoS flood | ......... | ....... | ......... |

Les trois attaques cryptographiques et d'identite sont neutralisees completement.
