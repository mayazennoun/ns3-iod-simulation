# Blockchain-Enhanced Secure Communication Framework for Real-Time Internet of Drones (IoD) Video Transmission

**Projet de Fin d'Etudes (PFE)**
Universite Badji Mokhtar Annaba — Laboratoire LRS
Annee universitaire 2025/2026


---

## Presentation

Ce projet propose et evalue un cadre de securite pour la transmission video en temps reel dans les reseaux Internet of Drones (IoD). Les drones sont utilises dans des missions critiques et leurs flux video circulent sur des canaux sans fil ouverts sans protection suffisante. Ce travail repond a ce manque.

Le cadre combine trois composants : un reseau blockchain prive pour la gestion des identites et le controle d acces, un chiffrement leger ChaCha20-Poly1305 pour proteger le contenu video, et un noeud Edge qui orchestre l authentification et relaie les donnees vers la station de controle au sol.

---

## Architecture du systeme

```
[Drone] --> Wi-Fi 802.11ac --> [Edge] --> filaire 1Gbps --> [GCS]
                                  |
                            [Blockchain PoA]
```

La video ne passe jamais par la blockchain. Seuls les evenements de controle y sont enregistres.

---

## Protocole d authentification (BAN Logic)

```
M1 : Drone -> Edge  : { ID_D, N_D } signe cle privee drone
M2 : Edge  -> Drone : { ID_E, N_E, N_D, K_DE, token } signe cle privee Edge
M3 : Drone -> Edge  : { N_E, Ack } signe cle privee drone
```

L'analyse BAN Logic confirme l'authentification mutuelle et la fraicheur de la cle sous les hypotheses standard.

---

## Configuration de simulation

| Parametre | Valeur |
|---|---|
| Simulateur | NS-3 v3.43 |
| Standard sans-fil | IEEE 802.11ac, 5 GHz |
| Modele de canal | Log-distance path loss + Nakagami fading |
| Largeur de bande | 40 MHz (N=5), 80 MHz (N=10, 20, 50) |
| Puissance emission | 20 dBm (N=5), 23 dBm (N>=10) |
| Protocole de routage | AODV |
| Mobilite UAV | Random Waypoint, 5-15 m/s, pause 0-2s |
| Debit video par UAV | 8 Mbps (N=5), 6 Mbps (N>=10) |
| Transport | UDP, paquets 1200 octets |
| Duree simulation | 300 secondes |
| Blockchain delay | 100 ms injecte au demarrage |
| Queue Edge | DropTail 100 paquets |

---

## Resultats — Scalabilite

| Scenario | Architecture | Debit moyen | L_total | Jitter | Pertes |
|---|---|---|---|---|---|
| N=5, 1 Edge | 40 MHz | 8.148 Mbps | 118 ms | 1.456 ms | 0.031% |
| N=10, 1 Edge | 80 MHz | 6.098 Mbps | 135 ms | 2.341 ms | 0.156% |
| N=20, 2 Edges | 80 MHz | 6.095 Mbps | 134 ms | 2.334 ms | 0.180% |
| N=20, 3 Edges | 80 MHz | ~6.09 Mbps | ~134 ms | ~2.3 ms | ~0.2% |
| N=50, 3 Edges | 80 MHz | en cours | | | |

---

## Resultats — Attaques

### Avec blockchain

| Attaque | Resultat | Impact legitimes |
|---|---|---|
| Eavesdropping | Bloquee — 0 paquet dechiffrable | Aucun |
| Replay | Bloquee — 0 paquet accepte | Aucun |
| Spoofing | Bloquee — 100% rejetes | Minimal |
| DoS 100 Mbps | Partiellement attenueee | Modere (-14%) |
| DDoS 3x50 Mbps | Partiellement attenueee | Severe (-34%) |

### Sans blockchain

| Attaque | Resultat |
|---|---|
| Eavesdropping | Chiffrement tient mais identite non verifiee |
| Replay | Vulnerable — pas de verification nonce |
| Spoofing | **Reussie — faux drone accepte a 7.91 Mbps** |
| DoS | Non attenueee — impact plus severe |

---

## Structure du depot

```
blockchain-iod-video-security/
├── README.md
├── LICENSE
├── .gitignore
├── requirements.txt
├── simulations/
│   ├── iod-simulation-5drones.cc
│   ├── iod-simulation-10drones.cc
│   ├── iod-simulation-20drones-2edges.cc
│   ├── iod-simulation-20drones-3edges.cc
│   ├── iod-simulation-50drones-3edges.cc
│   └── README.md
├── attack-scenarios/
│   ├── iod-attack-eavesdrop.cc
│   ├── iod-attack-replay.cc
│   ├── iod-attack-spoofing.cc
│   ├── iod-attack-dos.cc
│   ├── iod-attack-ddos.cc
│   └── README.md
├── attacks-without-blockchain/
│   ├── iod-attack-eavesdrop-nobc.cc
│   ├── iod-attack-replay-nobc.cc
│   ├── iod-attack-spoofing-nobc.cc
│   ├── iod-attack-dos-nobc.cc
│   └── README.md
├── results/
│   ├── raw/
│   │   ├── results-5drones.txt
│   │   ├── results-10drones.txt
│   │   └── results-20drones.txt
│   ├── figures/
│   │   ├── debit.png
│   │   ├── latence_ns3.png
│   │   ├── jitter.png
│   │   ├── pertes.png
│   │   └── resume_comparatif.png
│   └── courbes_ns3.py
├── docs/
│   ├── architecture.md
│   ├── simulation-setup.md
│   └── results-analysis.md
└── paper/
    └── figures/
```

---

## Comment lancer les simulations

```bash
cp simulations/iod-simulation-5drones.cc ~/ns-allinone-3.43/ns-3.43/scratch/iod-simulation.cc
cd ~/ns-allinone-3.43/ns-3.43
./ns3 build scratch/iod-simulation
./ns3 run scratch/iod-simulation
```

```bash
cd results/
python courbes_ns3.py
```

---

## Dependances

- NS-3 v3.43 (modules : wifi, mobility, internet, applications, flow-monitor, point-to-point, aodv)
- Python 3.x : `pip install -r requirements.txt`

---

## Reference

> M. Nafa, M. Zennoun, "Blockchain-Enhanced Secure Communication Framework for Real-Time Internet of Drones (IoD) Video Transmission", Laboratoire LRS, Universite Badji Mokhtar Annaba, 2025.

---

## Licence

Ce projet est publie sous la licence MIT.
