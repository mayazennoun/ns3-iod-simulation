# Analyse des resultats

Ce document presente une analyse detaillee des resultats obtenus lors des trois simulations NS-3 et leur interpretation dans le contexte du cadre de securite propose.

---

## Rappel des resultats bruts

Les resultats complets flow par flow sont disponibles dans `results/raw/`. Le tableau ci-dessous resume les moyennes calculees sur l'ensemble des drones pour chaque scenario.

| Scenario | Debit moyen | L_tx moyen | L_total moyen | Jitter moyen | Perte moyenne |
|---|---|---|---|---|---|
| N=5 (1 Edge) | 8.148 Mbps | 11.09 ms | 118.09 ms | 1.456 ms | 0.031 % |
| N=10 (1 Edge) | 6.098 Mbps | 28.06 ms | 135.06 ms | 2.341 ms | 0.156 % |
| N=20 (2 Edges) | 6.095 Mbps | 27.45 ms | 134.45 ms | 2.334 ms | 0.180 % |

---

## Analyse par scenario

### N=5 drones — 1 noeud Edge — 40 MHz

Le scenario a 5 drones represente les conditions optimales du systeme. Avec une demande agregee de 40 Mbps (5 x 8 Mbps) sur un canal de 40 MHz, la charge reste bien en dessous de la capacite theorique du canal 802.11ac. Le debit moyen atteint 8.148 Mbps par drone, soit une utilisation quasi complete du debit cible, et le taux de perte est inferieur a 0.04%.

La latence Wi-Fi L_tx est de 11.09 ms en moyenne. La latence totale L_total de 118 ms est dominee par le delai blockchain de 100 ms qui ne s'applique qu'au handshake initial et aux renouvellements de cle toutes les 60 secondes. Sur le chemin video continu, la latence effective est donc de 18 ms, largement en dessous du seuil de 150 ms admis pour le streaming video temps reel.

Ce scenario valide que la surcharge de securite introduite par le systeme est negligeable sur les performances du flux video.

### N=10 drones — 1 noeud Edge — 80 MHz

Le scenario a 10 drones montre une degradation moderee mais acceptable. La demande agregee passe a 60 Mbps (10 x 6 Mbps) sur un canal elargi a 80 MHz. Le debit moyen chute a 6.098 Mbps, ce qui correspond exactement au debit cible fixe pour ce scenario. La latence Wi-Fi augmente a 28.06 ms, refletant une contention plus elevee sur le canal partage entre 10 noeuds. La latence totale de 135 ms reste acceptable. Le taux de perte de 0.156% est negligeable pour une application video.

Ce scenario confirme que l'architecture avec un noeud Edge unique supporte efficacement jusqu'a 10 drones simultanees dans des conditions de mobilite realistes.

### N=20 drones — 2 noeuds Edge — 80 MHz — Architecture Multi-Edge

Le scenario a 20 drones utilise une architecture multi-Edge avec 2 noeuds Edge independants, chacun couvrant 10 drones sur un canal physique Wi-Fi separe. Cette approche elimine les interferences inter-reseaux et divise la charge par deux sur chaque point d'acces.

Les resultats demontrent l'efficacite de cette architecture : le debit moyen atteint 6.095 Mbps par drone, la latence totale est de 134.45 ms, le jitter de 2.334 ms et le taux de perte de 0.180%. Ces valeurs sont quasi identiques au scenario N=10, ce qui confirme que l'architecture multi-Edge restaure completement les performances independamment du nombre total de drones.

Ce resultat constitue la contribution principale de ce travail en matiere de scalabilite : en deployant autant de noeuds Edge que necessaire, chacun couvrant un sous-groupe de drones sur un canal physique dedie, le systeme peut passer a l'echelle sans degradation des performances.

---

## Evolution des metriques en fonction de N

### Debit

Le debit par drone diminue entre N=5 (8.148 Mbps) et N=10 (6.098 Mbps) en raison du partage du canal Wi-Fi entre plus de noeuds. Pour N=20 avec l'architecture multi-Edge, le debit reste stable a 6.095 Mbps, identique au scenario N=10, ce qui valide la solution architecturale.

### Latence

La latence totale augmente legerement entre N=5 (118 ms) et N=10 (135 ms) en raison de la contention Wi-Fi accrue. Pour N=20 multi-Edge, la latence de 134.45 ms est quasi identique au scenario N=10, confirmant l'efficacite de la separation des canaux physiques.

### Jitter

Le jitter augmente entre N=5 (1.456 ms) et N=10 (2.341 ms) puis se stabilise pour N=20 multi-Edge (2.334 ms). Ces valeurs restent toutes bien en dessous du seuil de 10 ms generalement admis pour le streaming video temps reel.

### Perte de paquets

Les pertes restent negligeables sur les trois scenarios : 0.031%, 0.156% et 0.180%. Cette stabilite demontre que le cadre de securite propose n'introduit pas de degradation reseau significative et que l'architecture multi-Edge maintient la qualite de service pour N=20.

---

## Conclusion

Les resultats de simulation demontrent que le cadre de securite propose introduit un overhead negligeable sur les performances reseau. La latence additionnelle due a la blockchain (100 ms) n'affecte que le chemin de controle et non le flux video continu. Le chiffrement ChaCha20 ajoute moins de 1 ms de traitement par trame.

L'architecture multi-Edge validee pour N=20 constitue une solution de scalabilite directement applicable aux deployements IoD a grande echelle. Elle confirme que le systeme peut passer a l'echelle en ajoutant des noeuds Edge supplementaires sans modification du mecanisme de securite sous-jacent.
