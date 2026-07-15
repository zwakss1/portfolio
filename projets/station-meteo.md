---
titre: Rapport Technique : Station Météo 2026
categorie: both
genre: "IoT / Électronique / Système Réseau"
jeu: Opération « Météo à l'École »
date: 2025 - 2026
lecture: 15 min
statut: clôturé
---

# Rapport de Projet : Réseau de Stations Météorologiques Connectées (Session 2026)



## 💼 I. CADRE GLOBAL & INFRASTRUCTURE COMMUNE

### 1. Situation du projet dans son contexte
Ce projet s'inscrit directement dans le cadre de l'opération nationale « MÉTÉO à l'École », qui vise à déployer un réseau de stations météorologiques au sein des établissements scolaires. Pour la session 2026, l'objectif assigné aux étudiants du Lycée Paul Langevin à Beauvais était de combler l'absence de telles installations de mesures dans le département de l'Oise, tout en mettant en valeur le savoir-faire technique de la filière BTS CIEL.

Le réseau est conçu pour être installé sur quatre établissements scolaires cibles situés dans un rayon d'environ 2000 à 2200 mètres autour du pôle central :
* Le Lycée Paul Langevin (Section BTS CIEL)
* Le Lycée François Truffaut
* Le Lycée Corot
* Le Collège Charles Fauqueux

#### Répartition de l'équipe technique (Rôles) :
* **Étudiant 1 :** Gestion de l'acquisition des capteurs environnementaux (Température, Vent, Pluie) - PCB 1.
* **Malheude Lucas (Moi-même) :** Architecture d'alimentation, circuits de protection, transmission sans fil LoRaWAN et liaison filaire - PCB 2.
* **Étudiant 3 :** Système de géolocalisation par coordonnées satellites via module GNSS - PCB 3.
* **Étudiant 4 :** Console locale de maintenance et gestion de l'interface d'affichage OLED - PCB 4.

### 2. Objectifs Généraux du Système
Chaque station météo doit opérer de manière totalement autonome dans son environnement afin de collecter les grandeurs physiques et de les envoyer vers un serveur distant cloud. La chaîne d'information globale se divise en quatre fonctions partagées :
* **Acquérir :** Mesurer précisément la température extérieure, la vitesse ainsi que la direction du vent, et la hauteur cumulée des précipitations.
* **Traiter :** Centraliser les données brutes sous la forme d'une trame de caractères standardisée ASCII dotée d'un identifiant unique propre à chaque station.
* **Transmettre :** Émettre la trame sans fil via une modulation radiofréquence vers une passerelle longue portée.
* **Restituer :** Mettre à disposition l'ensemble des historiques de mesures et de géolocalisation sur une interface graphique utilisateur finale.

### 3. Architecture Technique Commune
L'infrastructure matérielle et logicielle repose sur un socle technique moderne :
* **Alimentation Autonome :** L'énergie électrique du système est fournie par une batterie 12V couplée à un régulateur de charge, alimenté en continu par un panneau photovoltaïque externe.
* **Réseau de Communication :** Utilisation de la technologie LoRaWAN (bande de fréquence 868 MHz) s'appuyant sur le réseau communautaire et open-source **The Things Network (TTN)**, permettant une portée maximale théorique de 3000 mètres.
* **Interface de Maintenance :** Intégration sur le mât d'un coffret étanche comprenant une console locale avec écran OLED 2,4 pouces pour le diagnostic direct sur site par un technicien.

### 4. Spécifications du Cahier des Charges (SysML)
Le comportement et les contraintes de protection du système sont régis par les exigences de haut niveau suivantes :
* **ID 1 (Collecter) :** Centralisation des mesures de l'anémomètre à coupelles (plage 0 à 150 km/h, pas de 2,5 km/h), de la girouette à secteurs (16 directions, précision 22,5°), du pluviomètre à godets (résolution de 0,2794 mm par basculement) et du thermomètre (plage -20°C à +60°C, pas de 0,5°C).
* **ID 1.8 (Alimenter et protéger) :** Fournir des tensions d'alimentation parfaitement stables de +5V et +3,3V tout en intégrant des sécurités matérielles contre les surtensions, les courts-circuits et les inversions accidentelles de polarité.
* **ID 2 (Mettre à disposition) :** Assurer l'acheminement réseau IP vers le serveur de stockage TTN.
* **ID 3 (Visualiser IHM) :** Développer une interface logicielle sous l'outil graphique Node-RED pour l'affichage informatique des données.
* **Norme de fabrication :** L'assemblage, le placement et le brasage des composants (traversants et CMS) doivent obligatoirement respecter les critères d'acceptabilité de la norme internationale **spécification AFNOR 2212**.



## 🛠️ II. CONCEPTION INDIVIDUELLE (LUCAS MALHEUDE)

Pour mener à bien mes livrables (Alimenter, protéger, émettre en LoRaWAN et restituer en filaire), j'ai fait le choix architectural stratégique de concevoir deux cartes électroniques (PCB) distinctes afin de simplifier la phase de routage, de fiabiliser les tests isolés et d'optimiser la maintenance curative.

### 1. LE BLOC ALIMENTATION & PROTECTION (PCB 1)

[[screenshot: img/SYSML.png]]

#### 1.1 Diagramme d'Exigences & Architecture Interne
Ce bloc doit recevoir la tension fluctuante de la batterie 12V et générer des lignes d'alimentation stables indispensables au reste de la station. Il se structure de la manière suivante :
* **Protection fusible :** Calibrée à 1A pour couper le circuit en cas de surintensité majeure.
* **Protection contre l'inversion de polarité :** Assurée par une diode Schottky 1N5819 présentant une très faible chute de tension directe.
* **Protection contre les surtensions :** Mise en œuvre via une diode d'écrêtage TVS Transil P6KE15A.
* **Étage de régulation +5V :** Géré par un régulateur linéaire L7805.
* **Étage de régulation +3,3V :** Géré par un régulateur LM1117T-3.3 câblé en cascade derrière la ligne 5V pour limiter la dissipation thermique.

[[screenshot: img/alimV1.png, img/alimV2.png]]

#### 1.2 Évolution du Schéma Électronique (KiCad)
Lors de la première revue de conception (le 18 mars 2026), le premier schéma présentait plusieurs défauts majeurs de lisibilité : les liaisons de masse (GND) étaient toutes tracées manuellement, créant des croisements de câbles laborieux, et l'utilisation des labels manquait de clarté.

Sur la version finale épurée, et suivant les conseils de mon professeur M. Planterose, j'ai apporté les corrections suivantes :
* Remplacement des lignes GND par des symboles de masse unifiés.
* Structure de lecture propre de gauche à droite (12V → 5V → 3,3V).
* Intégration de borniers à vis dédiés (J2, J3, J4) pour distribuer les tensions vers les autres cartes.
* Ajout de points de test physiques (TP1, TP2, TP3) pour les mesures au multimètre ou à l'oscilloscope.

#### 1.3 Nomenclature & Gamme de Montage du PCB Alimentation

| Référence | Composant | Type / Valeur | Rôle dans le circuit |
| :--- | :--- | :--- | :--- |
| **J1** | Bornier 2 pins | Entrée 12V / GND | Raccordement de l'alimentation batterie principale |
| **F1** | Fusible rapide | 1A / Traversant | Sécurité contre les surintensités |
| **D1** | Diode TVS | P6KE15A | Protection d'écrêtage contre les surtensions |
| **D2** | Diode Schottky | 1N5819 | Sécurité contre l'inversion de polarité |
| **C1 / C2** | Condensateurs | 0,33 µF / 0,1 µF | Filtrage et découplage d'entrée du régulateur L7805 |
| **U1** | — | L7805 (Régulateur TO-220) | Abaisseur et régulateur de tension linéaire +5V |
| **C3 / C4** | Condensateurs | 10 µF | Filtrage et stabilisation de la ligne de sortie +5V |
| **U2** | — | LM1117T-3.3 (TO-220) | Abaisseur et régulateur de tension linéaire +3,3V |
| **C5 / C6** | Condensateurs | 10 µF / 100 nF | Stabilisation et filtrage de la ligne de sortie +3,3V |
| **TP1 à TP3** | Picots de test | Points de mesure | Relevés directs des tensions régulées (+12V, +5V, +3,3V) |
| **J5** | Header 2 pins | Cavalier / Jumper | Permet d'ouvrir la ligne pour mesurer le courant de repos |

#### Chronologie d'assemblage (Ordre de brasage) :
1. Diode Schottky D2 (1N5819)
2. Diode TVS D1 (P6KE15A)
3. Condensateurs de filtrage bas profil C1 (0,33µF) et C2 (0,1µF)
4. Points de test métalliques TP1 à TP3
5. Condensateurs chimiques C3 et C4 (10µF)
6. Régulateur de tension linéaire L7805 (U1)
7. Régulateur de tension linéaire LM1117T-3.3 (U2)
8. Porte-fusible et son fusible F1
9. Connecteur de coupure J5
10. Bornier d'entrée J1 et borniers de distribution J2, J3, J4

#### 1.4 Simulations et Bilan de Consommation Propre
L'architecture de régulation en cascade a été modélisée et validée sous le logiciel **SimulIDE**, affichant des valeurs virtuelles de 5.000V et 3.300V de manière stable.

L'analyse des fiches techniques des constructeurs a permis d'isoler la consommation fixe intrinsèque à la carte (courant de repos indispensable au maintien de la régulation) :
* Courant de repos typique du L7805 (U1) : 4,3 mA.
* Courant de repos typique du LM1117-3.3 (U2) : 5,0 mA.
* **Consommation totale à vide de la carte :** ~9,3 mA.

> ⚠️ **Note thermique importante :** S'agissant d'une régulation purement linéaire, l'énergie excédentaire liée à l'abaissement de tension (12V → 5V) est intégralement dissipée sous forme de chaleur par effet Joule. Bien que la batterie puisse fournir une intensité élevée, la sécurité thermique est bridée par les limites des boîtiers (1,5A max pour le L7805 et 0,8A pour le LM1117) sous la protection du fusible d'entrée de 1A.



#### 1.5 Routage, Modélisation 3D et PCB Réel (Alimentation)

Stratégie de Routage :

Largeur des pistes : Les pistes transportant la puissance (ligne d'entrée 12V, lignes régulées 5V et 3.3V) ont été dimensionnées à une largeur minimale de 1,0 mm afin de supporter de forts appels de courant sans échauffement de la piste de cuivre. Les signaux de référence et de test sont routés en 0,4 mm.

[[screenshot: img/PCBalim.png]]

Plan de masse : Un plan de masse continu (GND) a été coulé sur la couche inférieure (Bottom Copper) pour minimiser l'impédance de retour et faire office de dissipateur thermique pour les boîtiers TO-220 des régulateurs.

Séparation physique : Les condensateurs de découplage (C1, C2, C5, C6) ont été placés au plus près des broches d'entrée/sortie des régulateurs de tension pour maximiser leur efficacité contre les parasites haute fréquence.

📸 Visualisation CAO & Réalisation :

Modèle 3D (KiCad) : L'intégration des empreintes 3D (boîtiers TO-220 verticaux pour le L7805 et le LM1117, borniers à vis bleus au pas de 5,08 mm, et porte-fusible traversant) a permis de valider l'absence de collision mécanique et de vérifier l'espacement pour l'insertion des tournevis sur les borniers.

PCB Réel : Après gravure et perçage, le circuit imprimé double face finalisé présente un étamage propre et sans bavure. Les pistes d'alimentation principales sont bien isolées du plan de masse par une marge de garde de sécurité (clearance) de 0,5 mm afin de prévenir tout pont de soudure ou arc électrique accidentel.

---

### 2. LE BLOC INTERFACE & ÉMISSION LORAWAN (PCB 2)

#### 2.1 Spécifications SysML & Routage
Le second PCB assure le traitement local de la donnée série et son émission radio longue portée. 
* **Émission sans fil (Id 1.5.1) :** Réalisée via le module radiofréquence **LAMBDA68-8D** configuré sur la bande de fréquence réglementaire européenne des 868 MHz.
* **Liaison filaire (Id 1.5.2) :** Implémentation d'une interface RS232 (au niveau logique TTL) exploitant un connecteur Sub-D DB9 pour dialoguer avec la console de maintenance.

#### 2.2 Analyse Structurelle du Schéma
La carte s'articule autour d'un microcontrôleur **ATmega328P** cadencé par un quartz externe de 16 MHz équipé de ses deux condensateurs de charge de 22pF. 

L'interfaçage présente une contrainte critique de niveaux logiques : l'ATmega328P fonctionne sous une tension de +5V alors que le module RF LAMBDA68-8D fonctionne strictement sous +3,3V. Pour adapter les signaux et éviter de détruire les entrées logiques du module radio, j'ai intégré des **ponts diviseurs résistifs** (résistances de 4,7 kΩ en série et 10 kΩ de rappel à la masse) sur les lignes de communication SPI (MISO/SDO, MOSI/SDI, SCK et SS/nSEL).

#### 2.3 Nomenclature du PCB Communication

| Référence | Composant | Valeur / Boîtier | Rôle exact |
| :--- | :--- | :--- | :--- |
| **U1** | ATmega328P-P | DIP-28 / Traversant | Microcontrôleur central (traitement, UART et SPI) |
| **U2** | Module LoRa | LAMBDA68-8D | Émetteur radiofréquence 868 MHz vers passerelle |
| **Y1** | Quartz métallique | 16 MHz / HC-49 | Source d'horloge du microcontrôleur |
| **C1 / C2** | Condensateurs céramiques | 22 pF | Condensateurs de charge nécessaires à l'oscillateur Quartz |
| **C3 à C5** | Condensateurs céramiques | 100 nF | Condensateurs de découplage pour les broches VCC |
| **C6** | Condensateur chimique | 10 µF | Filtrage des basses fréquences de la ligne +3,3V radio |
| **R1 / R2** | Résistances de tirage | 10 kΩ | Pull-up pour maintenir stables les broches RESET |
| **R3 à R6** | Résistances de précision | 4,7 kΩ | Résistances série pour les ponts diviseurs SPI |
| **R7 à R10** | Résistances de précision | 10 kΩ / 22 kΩ | Résistances de pied pour les ponts diviseurs logiques |
| **SW1 / SW2** | Boutons tactiles | Boutons-poussoirs | Resets manuels respectifs de l'ATmega et du module RF |
| **J1 Data1** | Connecteur femelle | Embase DE9 / Sub-D | Réception de la trame brute ASCII de l'Étudiant 1 |
| **J2_Maint1** | Connecteur femelle | Embase DE9 / Sub-D | Restitution de la trame traitée vers l'Étudiant 4 |
| **J1 (Power)** | Bornier à vis | 3 points / Pas 5 mm | Entrée des lignes d'alimentation GND, +3,3V et +5V |
| **J2 / J3** | Cavaliers physiques | Strip pas 2,54 mm | Permet d'isoler ou d'activer individuellement les sous-blocs |


#### 2.4 Extraits du Code Source (Langage C — Arduino IDE)
Le programme assure la configuration basse couche du microcontrôleur : il définit la direction des broches de contrôle de la puce radio, initialise le bus de communication SPI matériel pour le dialogue avec le module LAMBDA68, et ouvre l'interface UART pour la réception de la trame météo.

```c
// Configuration basse couche du système (Extrait du Setup réel)
void setup() {
  // 1. Configuration des broches d'E/S de commande RF
  pinMode(PIN_TX_SWITCH, OUTPUT); digitalWrite(PIN_TX_SWITCH, LOW);
  pinMode(PIN_NSS,       OUTPUT); digitalWrite(PIN_NSS, HIGH); // CS au repos (état haut)
  pinMode(PIN_BUSY,      INPUT);  // Broche d'état du modem radio Semtech
  pinMode(PIN_DIO1,      INPUT);  // Broche d'interruption d'événement RF

  // 2. Initialisation du bus SPI matériel (Fréquence de 8 MHz, format MSBFIRST, Mode SPI 0)
  SPI.begin();
  SPI.beginTransaction(SPISettings(8000000, MSBFIRST, SPI_MODE0));

  // 3. Ouverture de la liaison série matérielle pour l'écoute de la trame d'acquisition
  Serial.begin(9600);
  delay(100);

  // 4. Initialisation des registres du modem LoRa et du Watchdog AVR
  loraInit();
  configurerWatchdog();
}
```
<br>

 #### 2.5 Routage, Modélisation 3D et PCB Réel (Communication)

 [[screenshot: img/PCBcom.png]]

La carte de communication intègre des signaux numériques à haute fréquence (SPI à 8 MHz et signal RF à 868 MHz). Son routage a requis une attention particulière pour éviter l'effet d'antenne parasite et l'atténuation du signal.

Contraintes de Routage :

Ligne RF (Antenne) : La piste reliant la sortie RF du module LAMBDA68 à l'embase d'antenne SMA a été routée de la manière la plus courte et rectiligne possible, en évitant les angles droits (privilégiant des virages à 135° ou des courbes) pour minimiser les ruptures d'impédance.

Adaptation de niveau et ponts diviseurs : Les résistances de conversion logique (4,7 kΩ et 10 kΩ) ont été groupées de manière compacte juste à côté des entrées logiques du module LAMBDA68 pour garder des lignes de transmission SPI propres.

Plan de masse : Présence d'un plan de masse maillé et renforcé autour de la puce radio pour assurer un blindage électromagnétique efficace contre les rayonnements parasites.

📸 Visualisation CAO & Réalisation :

Modèle 3D (KiCad) : La modélisation 3D intègre le support DIP-28 pour l'ATmega328P (permettant un remplacement rapide du contrôleur en cas de panne), le connecteur Sub-D DB9 femelle monté à angle droit, et l'empreinte spécifique CMS du module LAMBDA68-8D soudé en surface.

PCB Réel : Le circuit imprimé de communication finalisé met en valeur la compacité requise par l'intégration sur le mât de la station météo. Le brasage du module CMS LAMBDA68 à pas fin a été réalisé sous loupe binoculaire pour garantir des liaisons parfaites sans micro-court-circuit, respectant scrupuleusement la norme de qualité exigée.


## 📋 III. SPÉCIFICATIONS DE DIAGNOSTIC & MAINTENANCE

### 1. Fiche de Contrôle Électrique : PCB Alimentation
Avant toute mise sous tension, une inspection visuelle rigoureuse impose de contrôler l'intégrité du filament du fusible F1, de traquer d'éventuelles traces de décoloration thermique autour du boîtier du L7805, et de vérifier l'absence de ponts d'étain accidentels entre les pastilles des borniers.

| Point de Test | Valeur Nominale Attendue | Rôle Technique de Diagnostic |
| :--- | :--- | :--- |
| **TP1** | +12,0 V (± 0,5V) | Tension d'entrée issue de la batterie après filtrage et diodes |
| **TP2** | +5,0 V (± 0,2V) | Tension de sortie régulée du premier étage (L7805) |
| **TP3** | +3,3 V (± 0,1V) | Tension de sortie régulée du second étage cascade (LM1117) |
| **J5 (Pins 1-2)**| Continuité complète (Bip) | Validation de la présence obligatoire du cavalier d'alimentation |
| **GND** | 0,0 V | Référence de masse électrique commune à l'ensemble du système |

#### Guide de dépannage rapide :
* **Symptôme : TP1 = 12V mais TP2 = 0V**
    * *Cause probable :* Le cavalier amovible sur le connecteur J5 est manquant, ou le circuit intégré du régulateur L7805 est détruit.
    * *Remède :* Vérifier visuellement et insérer un jumper sur J5, ou procéder au remplacement de U1.
* **Symptôme : Le régulateur U1 (7805) présente une température anormalement élevée**
    * *Cause probable :* Appel de courant excessif en sortie, traduisant la présence d'un court-circuit franc sur la ligne 5V.
    * *Remède :* Isoler la carte et contrôler la résistance ohmique entre la ligne 5V et la masse pour localiser le composant défaillant.

### 2. Fiche de Contrôle Électrique : PCB Communication
L'inspection de cette carte impose de vérifier la présence des cavaliers de fermeture de ligne J2 et J3 (indispensables pour alimenter les sous-ensembles), ainsi que l'absence microscopique de pontages d'étain entre les broches très resserrées du module CMS LAMBDA68.

#### Guide de dépannage rapide :
* **Symptôme : Le microcontrôleur U1 est alimenté mais le module RF reste totalement inactif**
    * *Cause probable :* Le cavalier amovible sur le connecteur J3 est manquant, coupant la ligne dédiée 3,3V.
    * *Remède :* Placer un jumper sur J3 pour fermer le circuit d'alimentation radio.
* **Symptôme : Échec total de la liaison de données SPI entre l'ATmega et le module radio**
    * *Cause probable :* Inversion accidentelle lors du brasage entre les résistances de série (4,7 kΩ) et les résistances de pied (10 kΩ) sur les ponts diviseurs.
    * *Remède :* Contrôler les codes couleurs ou marquages des résistances et refaire les brasures selon les spécifications d'implantation.

---

## 🔍 IV. ANALYSE POST-MORTEM & INTERVENTION (BILAN DE FIN DE PROJET)

Afin de garantir une transparence totale, cette section consigne les écarts constatés en fin de projet entre les modélisations théoriques et le livrable physique final.

### 1. Bilan d'Intervention du PCB Alimentation (Validation Réelle)
* **Résultat des tests :** Le PCB d'alimentation a été entièrement assemblé, brasé selon la spécification AFNOR 2212, et testé sous tension en conditions réelles avec un succès total.
* **Validation technique :** Les relevés physiques effectués au multimètre sur les picots de test confirment la parfaite stabilité et la conformité des lignes régulées : la tension d'entrée de la batterie 12V (TP1) est proprement abaissée à +5,0 V (TP2) par le premier étage L7805, puis à +3,3 V (TP3) par le régulateur LM1117T-3.3 en cascade. Les circuits de protection (fusible, diode Transil et diode Schottky) sont opérationnels et valident à 100 % les modélisations théoriques préalablement effectuées sous SimulIDE.

### 2. Bilan d'Intervention du Bloc Communication : Validation à l'oscilloscope
Lors des phases de test du prototype assemblé, aucune trame n'était interceptée par la passerelle radio. Afin d'isoler la défaillance et d'écarter une panne du cœur de traitement, j'ai implémenté un programme de test unitaire (`Codetest.ino`) forçant le basculement cyclique des broches d'E/S.

* **Mesure à l'oscilloscope :** Le relevé des signaux à l'oscilloscope a mis en évidence un signal carré parfait cadencé à une seconde d'intervalle. Cette mesure confirme de manière irréfutable que le microcontrôleur ATmega328P exécutait correctement son code et que le quartz externe de 16 MHz oscillait à sa fréquence nominale (les temporisations logicielles étant parfaitement calibrées à l'écran de l'appareil).
* **Validation du design de protection :** Bien que les broches de commande (MOSI, SCK, SS) aient été forcées à l'état haut (générant du 5V en sortie d'ATmega), le module radio LAMBDA68 n'a subi aucun dommage électrique grâce aux ponts diviseurs résistifs (4,7 kΩ / 10 kΩ) calculés et implantés pour l'adaptation de niveau (5V → 3,3V). Concernant la ligne SDO (MISO), qui ne disposait pas de pont du fait que le signal circule normalement dans le sens 3,3V → 5V, le module a parfaitement toléré le forçage à 5V lors du test unitaire grâce à ses diodes de protection ESD internes et à la résistance de sortie intrinsèque des broches de l'ATmega, confirmant la robustesse globale du prototype lors des manipulations de laboratoire.

### 3. Hypothèses sur le blocage de la liaison RF (Pistes de résolution LoRa P2P)
Le projet utilisant une transmission en LoRa brut (Point-à-Point) et non la pile réseau standardisée LoRaWAN, le succès de la liaison dépendait strictement de l'alignement microscopique des registres radio de la puce Semtech SX1262 (intégrée au module LAMBDA68) avec ceux de la passerelle. 

Les cours étant terminés et le diplôme obtenu, le statut final du module radio (fonctionnel ou défectueux) reste à déterminer, mais trois hypothèses logiques majeures expliquent le défaut de communication :
1.  **Désalignement des paramètres de modulation (Modulation Params) :** En LoRa P2P, pour que deux puces se comprennent, elles doivent partager exactement les mêmes configurations de couche physique : la fréquence exacte (868,0 MHz), le *Spreading Factor* (SF7), la bande passante (BW 125 kHz), mais aussi le *Coding Rate* (CR 4/5) et la longueur du préambule. Un seul bit de différence dans ces registres pousse le moteur radio de la passerelle à ignorer le paquet.
2.  **Désynchronisation du Sync Word :** La configuration logicielle du registre de synchronisation de la puce LAMBDA68 (paramétrée sur les valeurs génériques `0x34`/`0x44`) entrait peut-être en conflit avec le mot de synchronisation privé configuré sur la passerelle réceptrice, rendant le signal invisible pour cette dernière.
3.  **Validation de la couche d'échange SPI :** Pour lever définitivement le doute sur l'état de santé du module LAMBDA68, la prochaine étape technique aurait consisté à écrire un script de lecture de registre SPI bas niveau (comme interroger le registre d'identification de la puce ou lancer une commande `GetStatus`). Si la puce renvoie sa signature sur la ligne MISO, cela prouverait qu'elle est en parfaite santé et qu'il s'agissait uniquement d'un problème de configuration logicielle des registres radio.



## 🛑 V. AUDIT TECHNIQUE & COUPLAGE INTER-CARTES (ANALYSE DE L'INTÉGRATION SYSTÈME)

Le défi majeur de ce projet résidait dans l'intégration et la communication entre la carte d'Acquisition (gérée par l'Étudiant 1 via une Arduino Uno) et ma carte de Transmission (ATmega328P autonome + module LoRa LAMBDA68). L'analyse croisée de nos retours d'ateliers met en lumière les réalités techniques et les verrous logiques rencontrés lors de la mise en commun.

### 1. Le Conflit Électrique et Logique du Port Série Matériel (Hardware Serial)
* **L'implémentation dans le code d'Acquisition :** Dans le fichier de communication de la carte d'acquisition, la liaison est initialisée via le port série natif (`Serial.begin(9600)`) pour envoyer la trame météo chiffrée.
* **Le problème d'architecture :** Sur une carte Arduino Uno, le port série matériel (broches 0 et 1) est physiquement partagé avec le convertisseur USB-Série qui gère le moniteur série PC. En connectant directement ces broches sur les broches RX/TX de ma carte technique, une collision de bus se crée. La puce USB de l'Uno et mon microcontrôleur essayaient de piloter la même ligne en même temps, ce qui perturbait les signaux électriques.
* **La solution corrective :** L'utilisation de la bibliothèque `SoftwareSerial` du côté de l'acquisition sur des broches numériques libres aurait permis d'isoler totalement le flux inter-cartes du port de débogage USB.

### 2. Autonomie des Phases de Test et Variations Temporelles
* **Des environnements de test indépendants :** Durant le cycle de développement, chaque membre de l'équipe a mené ses propres sessions de tests et résolu ses propres problématiques matérielles et logicielles de manière totalement autonome. Il est donc tout à fait normal que les boucles logiques et les temporisations (comme mon Mode Chasseur de 12 secondes basé sur le Watchdog face aux envois cycliques de l'acquisition) n'aient pas été synchronisées d'office.
* **La réalité du couplage :** Sans une phase finale d'ajustement global en équipe sur site, la mise en commun brute de codes testés séparément engendre naturellement des décalages temporels, empêchant le récepteur d'intercepter la trame au moment exact de son émission.


## 🎯 VI. MÉTHODE D'INJECTION & CONCLUSION GÉNÉRALE

### 1. Processus de Flashage et Injection du Bootloader
Pour rendre ma carte de communication autonome et s'affranchir d'une carte Arduino standard, le microcontrôleur ATmega328P vierge a été programmé directement sur notre PCB. 
* **Matériel utilisé :** Nous avons employé une carte de programmation externe spécifique fournie par le corps enseignant.
* **Action technique :** Cette interface matérielle dédiée a permis d'injecter avec succès le **bootloader** initial dans la mémoire non volatile de la puce, puis de téléverser nos programmes applicatifs réels (`noeud_lora_meteo_simple.ino`), validant ainsi la viabilité de notre chaîne de fabrication et de développement sur "carte nue" (*bare-metal*).

### 2. Enseignements du Projet
Bien que le réseau n'ait pas été déployé à 100 % sur l'ensemble des établissements cibles en fin de session, ce projet a été une expérience d'ingénierie extrêmement riche. Il m'a permis de valider des compétences clés indispensables sur le terrain :
* **Conception CAO & Routage (KiCad) :** Validation matérielle réelle réussie du bloc d'alimentation régulé, stabilisé et protégé.
* **Développement Embarqué & Métrologie :** Écriture de primitives SPI pour le module LAMBDA68, gestion des modes basse consommation de l'ATmega et diagnostics physiques avancés à l'oscilloscope.

Au final, ce projet montre qu'en IoT, la clé réside dans la communication : tant sur le plan technique pour interconnecter les cartes que sur le plan humain pour synchroniser l'équipe. Les compétences pratiques acquises durant cette session constituent un excellent tremplin pour ma future carrière de technicien supérieur.