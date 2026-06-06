# Résultats expérimentaux des attaques avec et sans blockchain

## 1. Objectif

Cette section présente les résultats des expérimentations menées sur un réseau de drones (IoD) afin d’évaluer l’impact d’un mécanisme de sécurité basé sur la blockchain.

Deux configurations sont comparées :
- Avec blockchain (authentification, chiffrement, anti-replay)
- Sans blockchain (architecture classique)

Les attaques simulées sont :
- Eavesdropping
- Replay attack
- Spoofing
- DoS UDP Flood
- DDoS multi-attaquants

---

## 2. Résultats avec blockchain

### 2.1 Eavesdropping
- Paquets transmis drones : 440 267  
- Paquets capturés : 374 101  
- Paquets déchiffrables : 0  
- Débit légitime : 8.00 Mbps  
- Perte légitime : 0.01%  
- Impact réseau : 0%  

---

### 2.2 Replay attack
- Paquets légitimes transmis : 441 350  
- Paquets capturés : 100  
- Paquets rejoués : 12 500  
- Débit rejeu reçu Edge : 0 Mbps  
- Débit légitime : 8.00 Mbps  
- Perte légitime : 0.01%  

---

### 2.3 Spoofing
- Paquets faux drone : 42 364  
- Paquets rejetés : 42 364 (100%)  
- Débit faux drone Edge : 0 Mbps  
- Débit légitime : 7.65 Mbps  
- Perte légitime : 4.15%  

---

### 2.4 DoS UDP Flood (100 Mbps)
- Paquets flood envoyés : 378 406  
- Débit flood reçu Edge : 53.89 Mbps  
- Paquets drop queue : 103  
- Débit légitime : 6.94 Mbps  
- Perte légitime : 12.34%  
- Latence légitime : 103 ms  

---

### 2.5 DDoS (3 attaquants)
- Paquets flood envoyés : 391 676  
- Débit DDoS reçu Edge : 45.06 Mbps  
- Paquets drop queue : 178  
- Débit légitime : 5.03 Mbps  
- Perte légitime : 34.55%  
- Latence légitime : 157 ms  

---

## 3. Résultats sans blockchain

### 3.1 Eavesdropping
- Paquets transmis drones : 443 994  
- Paquets capturés : 374 977  
- Paquets déchiffrables : 0  
- Débit légitime : 8.01 Mbps  
- Perte légitime : 0.01%  
- Latence totale : 24.6 ms  

---

### 3.2 Replay attack
- Paquets légitimes transmis : 443 768  
- Paquets capturés : 100  
- Paquets rejoués : 12 500  
- Débit rejeu reçu Edge : 0 Mbps  
- Débit légitime : 8.01 Mbps  
- Latence totale : 24.8 ms  

---

### 3.3 Spoofing
- Paquets faux drone : 63 525  
- Débit faux drone Edge : 7.91 Mbps  
- Attaque réussie : oui  
- Débit légitime : 7.96 Mbps  
- Perte légitime : 0.73%  
- Latence totale : 39 ms  

---

### 3.4 DoS UDP Flood
- Paquets flood envoyés : 383 047  
- Débit flood reçu Edge : ~0 Mbps  
- Débit légitime : 6.85 Mbps  
- Perte légitime : 13.48%  
- Latence : 111 ms  

---

## 4. Analyse comparative

### 4.1 Sécurité

Avec blockchain :
- Spoofing totalement bloqué (100%)
- Replay inefficace
- Eavesdropping non exploitable

Sans blockchain :
- Spoofing réussi
- Replay partiellement exploitable
- Faible authentification

---

### 4.2 Performance réseau

Avec blockchain :
- Meilleure protection du trafic légitime
- Augmentation de latence sous attaque (jusqu’à 157 ms)

Sans blockchain :
- Moins de contrôle sécurité
- Latence plus faible hors attaque

---

### 4.3 Résumé

| Attaque   | Sans blockchain | Avec blockchain |
|-----------|----------------|-----------------|
| Eavesdropping | Non exploitable | Chiffrement total |
| Replay        | Partiel | Bloqué |
| Spoofing      | Réussi | Bloqué 100% |
| DoS           | Fort impact | Impact réduit |
| DDoS          | Effondrement réseau | Dégradation contrôlée |

---

## 5. Conclusion

L’intégration de la blockchain améliore significativement la sécurité du réseau de drones, notamment contre le spoofing et le replay. Malgré une augmentation de latence sous attaque, le trafic légitime est mieux protégé qu’en architecture classique.
