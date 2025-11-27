# CONCEPTION-TESTEUR-PILES-PARTIE-1-DFTA247
Testeur

Testeur de piles AA/AAA intelligent — ATtiny85

Ce projet propose un testeur de piles AA / AAA basé sur un ATtiny85, capable d’évaluer une pile selon trois critères :

Tension à vide

Tension sous charge (≈100 mA)

Analyse dynamique : stabilité + ΔV

Il détecte aussi les inversions de polarité, les surtensions et les anomalies de courant.
L’affichage se fait à l’aide de 3 barrettes NeoPixel, soit 3 × 4 LED sur le PCB final.

Le test est déclenché automatiquement au démarrage (ou via le bouton de reset matériel).
Il fonctionne en mode one-shot : un seul test est effectué, sans boucle.
