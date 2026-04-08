# Analyse des resultats

Ce document presente une analyse detaillee des resultats obtenus lors des trois simulations NS-3 et leur interpretation dans le contexte du cadre de securite propose.

---

## Rappel des resultats bruts

Les resultats complets flow par flow sont disponibles dans `results/raw/`. Le tableau ci-dessous resume les moyennes calculees sur l'ensemble des drones pour chaque scenario.

| Scenario | Debit moyen | L_tx moyen | L_total moyen | Jitter moyen | Perte moyenne |
|---|---|---|---|---|---|
| N = 5  | 8.148 Mbps | 11.09 ms | 118.09 ms | 1.456 ms | 0.031 % |
| N = 10 | 6.098 Mbps | 28.06 ms | 135.06 ms | 2.341 ms | 0.156 % |
| N = 20 | 1.452 Mbps | 262.07 ms | 369.07 ms | 4.574 ms | 76.47 % |

---

## Analyse par scenario

### N = 5 drones

Le scenario a 5 drones represente les conditions optimales du système. Avec une demande agregee de 40 Mbps (5 x 8 Mbps) sur un canal de 40 MHz, la charge reste bien en dessous de la capacite theorique du canal 802.11ac. Les resultats confirment cette observation : le debit moyen atteint 8.148 Mbps par drone, soit une utilisation quasi complete du debit cible, et le taux de perte est inferieur a 0.04 %.

La latence Wi-Fi L_tx est de 11.09 ms en moyenne, ce qui inclut les delais de transmission physique, de propagation et de file d'attente dans les interfaces reseau. La latence totale L_total de 118 ms est dominee par le delai blockchain de 100 ms, qui ne s'applique qu'au handshake initial et aux renouvellements de cle toutes les 60 secondes. Sur le chemin video continu, la latence effective est donc de 18 ms, ce qui est largement en dessous du seuil de 150 ms generalement admis pour le streaming video en temps reel.

Le jitter de 1.456 ms indique un flux tres stable, ce qui est coherent avec la faible charge du canal.

Ce scenario valide que la surcharge de securite introduite par le système (blockchain + ChaCha20) est negligeable sur les performances du flux video.

### N = 10 drones

Le scenario a 10 drones montre une degradation moderee mais qui reste dans des limites acceptables pour une application de streaming video. La demande agregee passe a 60 Mbps (10 x 6 Mbps) sur un canal elargi a 80 MHz.

Le debit moyen chute a 6.098 Mbps, ce qui correspond au debit cible fixe pour ce scenario. La latence Wi-Fi augmente a 28.06 ms, refletant une contention plus elevee sur le canal partage entre 10 noeuds. La latence totale de 135 ms reste acceptable. Le taux de perte de 0.156 % est negligeable pour une application video.

Le jitter de 2.341 ms reste faible et n'affecterait pas la qualite du flux video percu par l'operateur.

Ce scenario confirme que l'architecture proposee avec un noeud Edge unique supporte efficacement jusqu'a 10 drones simultanees dans des conditions de mobilite realistes.

### N = 20 drones

Le scenario a 20 drones revele la limite de scalabilite de l'architecture a noeud Edge unique. Avec une demande agregee de 120 Mbps (20 x 6 Mbps) sur un canal 802.11ac de 80 MHz fonctionnant en half-duplex, le mecanisme CSMA/CA engendre une contention severe entre les 20 noeuds qui tentent d'acceder simultanement au canal.

Le debit moyen s'effondre a 1.452 Mbps par drone, soit moins de 25 % du debit cible. La latence Wi-Fi explose a 262 ms en raison des longues files d'attente formees aux interfaces des noeuds. Le taux de perte de 76.47 % rend le flux video inutilisable en conditions reelles.

Il est important de noter que cette degradation n'est pas due au mecanisme de securite mis en place. Le chiffrement ChaCha20 et l'authentification blockchain fonctionnent correctement — les paquets qui arrivent a destination sont bien chiffres et authentifies. La saturation est exclusivement un phenomene de couche reseau lie au partage du medium sans fil.

Ce resultat identifie clairement la frontiere de scalabilite du système et motive le deploiement de plusieurs noeuds Edge pour les scenarios a grande echelle, chaque Edge couvrant un sous-ensemble de drones.

---

## Evolution des metriques en fonction de N

### Debit

Le debit par drone diminue progressivement avec N. Entre N=5 et N=10, la degradation est limitee (-25 %) grace a l'augmentation du canal de 40 a 80 MHz. Entre N=10 et N=20, la degradation est brutale (-76 %) car la charge agregee depasse la capacite effective du canal en conditions de contention CSMA/CA.

### Latence

La latence totale reste stable entre N=5 (118 ms) et N=10 (135 ms), ce qui confirme que le système absorbe bien le passage de 5 a 10 drones. Elle augmente fortement a N=20 (369 ms) en raison de l'accumulation dans les files d'attente des interfaces Wi-Fi.

### Jitter

Le jitter augmente regulierement avec N : 1.456 ms, 2.341 ms, 4.574 ms. Meme a N=10, le jitter reste tres faible et n'affecterait pas la qualite perceptible du flux video. A N=20, le jitter de 4.574 ms est plus eleve mais reste secondaire face au taux de perte de 76 %.

### Perte de paquets

C'est la metrique la plus discriminante. Elle reste quasi nulle jusqu'a N=10 (0.031 % et 0.156 %) puis saute a 76.47 % pour N=20. Cette discontinuite confirme qu'il existe un seuil de saturation entre 10 et 20 drones pour cette configuration.

---

## Conclusion

Les resultats de simulation demontrent que le cadre de securite propose introduit un overhead negligeable sur les performances reseau pour des scenarios de taille raisonnable (N inferieur ou egal a 10). La latence additionnelle due a la blockchain (100 ms) n'affecte que le chemin de controle et non le flux video continu. Le chiffrement ChaCha20 ajoute moins de 1 ms de traitement par trame.

La limite de scalabilite identifiee a N=20 avec un noeud Edge unique est une contrainte architecturale et non une limitation du mecanisme de securite. La solution naturelle est le deploiement de plusieurs noeuds Edge en parallele, chacun couvrant un sous-groupe de drones, ce qui constitue une perspective d'evolution directe de ce travail.
