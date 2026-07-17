---
titre: Shield de Prototypage : ESP32-S3 ProtoShield
categorie: hardware
genre: "Électronique / Conception CAO"
date: 2026
lecture: 5 min
statut: en-cours 
---

# ESP32-S3 ProtoShield

## I. CONTEXTE
Pourquoi ce shield : le câblage répété au Dupont volant pour chaque projet ESP32-S3 devenait une vraie perte de temps. J'ai donc conçu ce support standardisé, propre et réutilisable.

---

## II. CONCEPTION

### 1. Routage & Implantation
- **Brochage natif :** Choix d'un mappage 1:1 identique à la dev board officielle (format standard DevKitC-1). Tous les GPIO, lignes d'alimentation (3V3, 5V) et GND sont déportés sur des connecteurs externes.
- **Pistes propres :** Routage parallèle et soigné sur la couche de cuivre arrière (B.Cu), garantissant une séparation nette et un rendu symétrique.
- **Sérigraphie optimisée :** Ajout de labels clairs pour chaque pin sur la couche F.Silkscreen. Les étiquettes sont placées vers l'extérieur de la carte pour rester parfaitement lisibles même lorsque l'ESP32-S3 est clipsé au centre.

### 2. Astuces CAO sous KiCad
- **Alignement 3D :** Utilisation des modèles 3D standards KiCad pour les barrettes femelles 1x22 (PinSocket_1x22_P2.54mm_Vertical.step). Alignement parfait obtenu grâce à un décalage des axes.
- **Finition des contours :** Adieu les coins pointus. Utilisation de l'outil automatique de KiCad (Modifications de forme -> Arrondi) sur la couche Edge.Cuts pour obtenir des angles lisses et professionnels de 3 mm de rayon.
- **Validation :** Exécution du DRC (Design Rule Check) validée avec 0 erreur et 0 avertissement avant l'exportation.

---

## III. FABRICATION

### 1. Spécifications du PCB (JLCPCB)
L'export des fichiers Gerber (incluant les couches de cuivre, masques, sérigraphie et fichiers de perçage distincts PTH/NPTH) a été téléversé chez JLCPCB avec la configuration suivante :
- **Dimensions :** 79,82 x 65,56 mm (Double couche FR-4, épaisseur standard de 1,6 mm).
- **Finition esthétique :** Vernis de protection Bleu avec sérigraphie blanche haute netteté.
- **Soudabilité :** Finition de surface HASL (avec plomb) sélectionnée pour faciliter la soudure manuelle à basse température (soudures plus brillantes, meilleur étalement de l'étain).
- **Économie d'échelle :** Commande de 5 exemplaires avec expédition via Global Standard Direct Line à un coût logistique ultra-optimisé de 1,32 €.

### 2. Sourcing des composants (LCSC)
Pour équiper les 5 platines, l'approvisionnement des barrettes de connexion s'est fait en direct chez LCSC :
- **Composant :** 10x connecteurs femelles Megastar 22 broches, pas de 2,54 mm, montage traversant (MPN: ZX-PM2.54-1-22PY / LCSC Part #: C7499337).
- **Logistique :** Utilisation de la fonction d'envoi groupé "Combine with JLCPCB Order". Les composants LCSC sont directement intégrés au colis de l'usine de PCB, faisant tomber les frais de port de LCSC à 0 $.

---

## IV. ASSEMBLAGE ET VALIDATION
- [à compléter après brasage : contrôle visuel, qualité du vernis bleu, premier branchement et test de continuité de l'ESP32-S3]

---

## V. ENSEIGNEMENTS
- [à compléter en fin de projet : ce que t'as appris niveau CAO, gestion des bibliothèques d'empreintes customs ou outillage KiCad]

---

<div class="download-bar">
  <a class="btn-download" href="projets/KICAD/ESP32S3-ProtoShield - Kicad-10.0.zip" download>
    ⬇️ Projet KiCad (.zip)
  </a>
  <a class="btn-download" href="projets/KICAD/Gerber/esp32-s3-protoshield-gerbers.zip" download>
    ⬇️ Gerbers JLCPCB (.zip)
  </a>
</div>