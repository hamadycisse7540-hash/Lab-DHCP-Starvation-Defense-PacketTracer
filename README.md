# Lab : Défense contre la DHCP Starvation (DHCP Snooping & Port-Security)

Rapport de laboratoire réalisé dans le cadre du **Mentorat Cybersécurité CYB6 (FORCE N)**.
Ce volet passe du côté **défense** : il montre, captures à l'appui, comment un switch Cisco
durci neutralise l'attaque **DHCP Starvation**. La démonstration est faite sous **Cisco Packet
Tracer**, qui simule des switches Cisco complets (DHCP snooping et port-security).

Il complète les deux rapports d'attaque :
- version **VyOS / VirtualBox** (serveur DHCP Kea),
- version **routeur Cisco / GNS3** (image IOS c7200).

> ⚠️ Toutes les manipulations ont été réalisées sur une **maquette simulée et isolée**, sans
> aucun lien avec un réseau réel. L'usurpation de MAC n'est montrée que pour valider la
> contre-mesure. N'appliquez jamais ces techniques sur un réseau sans autorisation écrite.

## Ce lab en 3 volets

Ce laboratoire DHCP Starvation est publié en trois dépôts complémentaires :

1. Attaque, serveur DHCP **VyOS** sous VirtualBox : [Lab-DHCP-Starvation](https://github.com/hamadycisse7540-hash/Lab-DHCP-Starvation)
2. Attaque, **routeur Cisco** sous GNS3 : [Lab-DHCP-Starvation-Cisco-GNS3](https://github.com/hamadycisse7540-hash/Lab-DHCP-Starvation-Cisco-GNS3)
3. Défense, **DHCP snooping et port-security** sous Packet Tracer : [Lab-DHCP-Starvation-Defense-PacketTracer](https://github.com/hamadycisse7540-hash/Lab-DHCP-Starvation-Defense-PacketTracer) **(ce dépôt)**

## Environnement

| Équipement | Rôle | IP | Raccordement |
|---|---|---|---|
| R1 (Cisco 1941) | Serveur DHCP / passerelle | `192.168.6.1` | `Gig0/0` → switch `Fa0/1` |
| Switch0 (2960) | Switch d'accès (défense) | — | `Fa0/1` de confiance |
| PC0 | Client légitime | `192.168.6.2` (DHCP) | `Fa0/2` |
| PC1 | Attaquant | `192.168.6.3` (DHCP) | `Fa0/3` |

Topologie : `R1 (1941) — Switch0 (2960) — PC0 / PC1`, réseau `192.168.6.0/24`. Le port `Fa0/1`
(vers le vrai serveur DHCP) est le port de confiance ; `Fa0/2` et `Fa0/3` sont des ports
clients non approuvés.

## Contenu du lab

1. **Tâche 1. Maquette et serveur DHCP fonctionnel** : configuration du routeur en serveur DHCP
   (pool L3SIMAC), les deux PC reçoivent une adresse. État de référence.
2. **Tâche 2. Mise en place des contre-mesures** : `DHCP snooping` (port de confiance, limite
   de débit) et `port-security` (une seule MAC par port, violation `shutdown`), avec
   vérification.
3. **Tâche 3. Validation de la défense** : simulation de l'usurpation de MAC (cœur de la
   starvation), le port bascule en `err-disabled`, preuve via `show port-security`.
4. **Annexe** : récapitulatif des commandes utilisées, de leurs rôles et de leurs options.

## Structure du dépôt

```
.
├── Rapport_Lab_DHCP_Starvation_Defense.tex       # Source LaTeX du rapport
├── Rapport_Lab_DHCP_Starvation_Defense.pdf       # Rapport compilé
├── Rapport_Lab_DHCP_Starvation_Defense.pdf.asc   # Signature GPG détachée du PDF
├── signature.tex                                 # Bloc signature (auteur + empreinte GPG)
├── HAMADY_CISSE_public_key.asc                   # Clé publique GPG de l'auteur
├── images/                                       # Captures d'écran du lab
├── maquette/
│   └── Lab_Defense_DHCP.pkt                       # Projet Cisco Packet Tracer (maquette)
├── LICENSE
└── README.md
```

## Ouvrir la maquette

Le fichier `maquette/Lab_Defense_DHCP.pkt` est le projet **Cisco Packet Tracer** (version
9.0.1) utilisé pour ce lab. Il s'ouvre avec Packet Tracer (gratuit, compte NetAcad). La
maquette est fournie en état sain, la défense déjà configurée sur le switch : le routeur
distribue le DHCP, les deux PC ont une adresse, le DHCP snooping et la port-security sont en
place.

Pour rejouer la démonstration, suivez la **Tâche 3** du rapport : donnez une fausse adresse MAC
à `PC1` (onglet *Config*, interface `FastEthernet0`), relancez un `ping` vers `192.168.6.1`, et
observez le port `Fa0/3` basculer en `err-disabled`.

## Compilation

```bash
pdflatex Rapport_Lab_DHCP_Starvation_Defense.tex
pdflatex Rapport_Lab_DHCP_Starvation_Defense.tex   # 2e passe pour le sommaire et l'annexe
```

## Vérifier l'authenticité

Le PDF du rapport est signé numériquement (GPG) :

```bash
gpg --import HAMADY_CISSE_public_key.asc
gpg --verify Rapport_Lab_DHCP_Starvation_Defense.pdf.asc Rapport_Lab_DHCP_Starvation_Defense.pdf
```

La vérification doit afficher `Good signature from "HAMADY CISSE ..."`. L'empreinte de la clé
est :

```
A6F4 0DFD EE3E B7AE 80B3  6E77 361C 9E02 B790 303E
```

## Auteur

HAMADY CISSE, Mentorat Cybersécurité CYB6, Groupe 3.

## Licence

© 2026 HAMADY CISSE. Ce travail est publié sous licence
**[Creative Commons Attribution - Pas d'Utilisation Commerciale 4.0 International (CC BY-NC 4.0)](https://creativecommons.org/licenses/by-nc/4.0/)**.
Voir le fichier [`LICENSE`](LICENSE) pour les termes complets.
