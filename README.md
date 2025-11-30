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
Faible consommation
Test unique → fiable et reproductible

📡 Principe de fonctionnement
1. Test à vide
Le microcontrôleur mesure la tension directement sur la borne positive de la pile.
Une série de mesures est effectuée pour vérifier la stabilité du signal.

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

🔌 Alimentation & protections

Le testeur peut être alimenté via :
USB-C
Micro-USB
Jack DC
Power bank
Alimentation de labo

Toutes les sources passent par des diodes Schottky avant d’attaquer le régulateur 3,3 V.

Protection inversion pile

Deux niveaux :
Protection analogique A1
R1 = 1 kΩ
D1 = diode Schottky montée en inverse
→ limite la tension à environ –0,15 V (safe)

Détection matérielle via MOSFET canal P
→ LED inversion de polarité

🖥️ Affichage NeoPixel & logique 3,3 V → 5 V

Le signal DATA du WS2812B est envoyé directement depuis PB0 en 3,3 V, sans convertisseur de niveau.
Pourquoi cela fonctionne ?
Les WS2812B reconnaissent un “1” logique dès ~0,7 × VDD
Beaucoup de modules acceptent sans problème 3,2–3,4 V en entrée
Le câble est très court → pas de pertes
Le test réel confirme un fonctionnement 100 % fiable
Une résistance série R2 = 220 Ω protège le premier pixel, conformément aux recommandations du fabricant.
Des condensateurs de 100 nF seront placés proche de chaque LED sur le PCB final.

🔊 Buzzer

Simple signalisation sonore :
1 bip → test à vide
2 bips → test en charge
3 bips → diagnostic final
3 bips rapides → surintensité

🛡️ Protection avancée & sécurité

Le code protège contre :
inversion de polarité
pile absente
surtension (>1,65 V)
instabilité lecture
surintensité
incohérences entre tension à vide et en charge
ΔV trop important

🧠 Description du code

Le programme est écrit en mode one-shot :
tout est exécuté dans setup(), et loop() reste vide.

Fonctions principales :
readADC_stable() → double lecture pour stabiliser l’ADC
mesurerStabilite() → 6 mesures + analyse min/max
lireVBAT() → conversion analogique → tension en volts
classerPile() → renvoie 0 / 1 / 2 selon les seuils
bipBuzzer() → signal sonore configurable
Toutes les phases du test sont clairement commentées.

📏 Seuils par défaut

À vide :

< 1,10 V → faible
< 1,36 V → moyenne
≥ 1,36 V → bonne

En charge : mêmes seuils (modifiable facilement).

Diagnostic ΔV :

ΔV < 0,25 V + NV=2 + NC=2 → excellent
ΔV < 0,30 V + NV>0 + NC>0 → correct
Sinon → mauvais


Une vidéo spécifique montrera la conception et la fabrication du PCB.



📣 Licence

Projet open-source : libre d’usage, modification et amélioration.


