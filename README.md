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

📘 Caractéristiques principales

ATtiny85 alimenté en 3,3 V via LDO MCP1700

Test complet en 3 phases : à vide, en charge, diagnostic final

Détection d’inversion de polarité par hardware

Protection renforcée des entrées analogiques

Détection de surintensité

3 barrettes de LED adressables pour un affichage clair

Signalisation par buzzer (1, 2 ou 3 bips selon les étapes)

Code simple à adapter, avec seuils facilement modifiables

Très faible consommation

Test unique → fiable et reproductible

📡 Principe de fonctionnement
1. Test à vide

Le microcontrôleur mesure la tension directement sur la borne positive de la pile.
Une série de lectures est effectuée pour vérifier la stabilité du signal.

Affichage (Barrette 1) :

| LED | Couleur | Signification    |
| --- | ------- | ---------------- |
| 0   | Rouge   | Pile très faible |
| 1   | Orange  | Pile faible      |
| 2   | Vert    | Pile correcte    |
| 4   | Vert    | Mesure stable    |
| 4   | Bleu    | Mesure instable  |

2. Test en charge

La charge est appliquée via un MOSFET canal N piloté par PB1.
La chute de tension dans la résistance RSENSE = 2,2 Ω permet de mesurer le courant réel.

Mesure effectuée après stabilisation, puis coupure de la charge.

Affichage (Barrette 2) :

| LED | Couleur | Signification                |
| --- | ------- | ---------------------------- |
| 8   | Rouge   | Pile trop faible sous charge |
| 9   | Orange  | Moyenne                      |
| 10  | Vert    | Bonne                        |
| 12  | Vert    | Tension stable               |
| 12  | Bleu    | Instable                     |

Surintensité (>0,20 A) → alarme immédiate rouge + 3 bips + arrêt du test.

3. Diagnostic final : ΔV + cohérence

Le diagnostic final compare la tension à vide et la tension en charge :

ΔV = Vvide – Vcharge

Il applique une logique stricte pour éviter les incohérences.
Le résultat est affiché sur la barrette 3.

| LED | Couleur          | Condition                     | Signification                          |
| --- | ---------------- | ----------------------------- | -------------------------------------- |
| 18  | Vert             | NV = 2 & NC = 2 & ΔV < 0.25 V | Pile excellente                        |
| 17  | Orange           | NV > 0 & NC > 0 & ΔV < 0.30 V | Pile correcte                          |
| 16  | Rouge            | Sinon                         | Pile mauvaise / incohérente            |
| 20  | Bleu             | Vvide > 1,65 V                | Surtension (pile lithium 1,5V régulée) |
| 20  | Rouge clignotant | Vbat < 50 mV ou inversion     | Pile absente ou inversée               |
| 20  | Rouge fixe       | Fin de l’alarme surtension    | —                                      |
