
Reseau

# RAPPORT DE PROJET D’INFRASTRUCTURE RESEAU

## Introduction

> L’entreprise Eyes4-IT s’est engagée dans la mise en place d’une nouvelle infrastructure réseau afin de répondre à ses besoins croissants en termes de performance, de sécurité et de gestion du trafic. Le projet vise à segmenter le réseau en utilisant des VLANs, à isoler les départements, à mettre en place le routage inter-VLAN, à assurer la redondance, et à renforcer la sécurité. Ce rapport présente en détail l’architecture mise en œuvre.

## Architecture de l’Infrastructure

> ### Segmentation du Réseau
> 
> Nous avons divisé le réseau en plusieurs segments logiques, chacun associé à un département spécifique de l’entreprise.
> 
> -   VLAN 10 (Développement) : Réseau 192.168.10.0/28
> -   VLAN 20 (Marketing) : Réseau 192.168.20.0/28
> -   VLAN 30 (Ressources Humaines) : Réseau 192.168.30.0/29
> 
> ### Composants Utilisés
> 
> -   Switches Layer 2 : Un switch Layer 2 pour la VLAN 10 (Développement) et un autre pour les VLANs 20 et 30 (Marketing et Ressources Humaines).
> -   Switches Layer 3 : Deux switches Layer 3 configurés en HSRP, chacun connecté aux switches Layer 2 correspondants.
> -   SVIs (Interfaces Virtuelles de Sous-Réseau) : Trois SVIs configurées sur chaque switch Layer 3, une pour chaque VLAN.
> -   IPs Virtuelles des SVIs : Chaque SVI dispose d’une adresse IP virtuelle pour le routage inter-VLAN.
> -   Priorité des Switches Layer 3 : Un switch Layer 3 en priorité (standby 100) et l’autre en secours (standby 50).
> -   Serveurs DHCP : Configuration de serveurs DHCP pour chaque VLAN depuis les switches Layer 3.
> -   ACLs (Listes de Contrôle d’Accès) : Mise en place de règles ACL pour restreindre la communication entre les SVIs conformément aux exigences de sécurité.

## Plan Réseau et Nommage

> ### VLAN 10 (Développement)
> 
> ----------
> 
> -   Réseau : 192.168.10.0/28
> -   Plage d’IPs : 192.168.10.1 à 192.168.10.14
> -   Passerelle : 192.168.10.1
> 
> #### Postes de Travail
> 
> -   Nommage des Postes : PC-DEV-XX (XX de 01 à 10)
> 
> ----------

> ### VLAN 20 (Marketing)
> 
> ----------
> 
> -   Réseau : 192.168.20.0/28
> -   Plage d’IPs : 192.168.20.1 à 192.168.20.14
> -   Passerelle : 192.168.20.1
> 
> #### Postes de Travail
> 
> -   Nommage des Postes : PC-MKT-XX (XX de 01 à 05)
> 
> ----------

> ### VLAN 30 (Ressources Humaines)
> 
> ----------
> 
> -   Réseau : 192.168.30.0/29
> -   Plage d’IPs : 192.168.30.1 à 192.168.30.6
> -   Passerelle : 192.168.30.1
> 
> #### Postes de Travail
> 
> -   Nommage des Postes : PC-RH-XX (XX de 01 à 02)
> 
> ----------

> ### Équipements Réseau
> 
> ----------
> 
> -   Nommage Switch Niveau 2 : SW-L2-XX (XX de 01 à 02)
> -   Nommage Switch Niveau 3 : SW-L3-XX (XX de 01 à 02)
> 
> ----------

## Mise en Place des VLANs et des SVI

Dans cette section, nous allons explorer la mise en place des VLANs (Virtual Local Area Networks) et des SVI (Switched Virtual Interfaces) pour segmenter le réseau en différents domaines logiques et permettre une communication efficace entre eux. La création de VLANs et de SVI

> ### VLAN 10 (Développement)
> 
> ----------
> 
> **Exemple de Mise en Place d’un VLAN (Développement) :**
> 
> 1.  Configuration du VLAN sur le switch Layer 2 :
>     
>     ```bash
>     Switch-L2(config)# vlan 10
>     Switch-L2(config-vlan)# name Developpement
>     Switch-L2(config-vlan)# exit
>     
>     ```
>     
> 2.  Configuration de l’interface VLAN sur le switch Layer 3 :
>     
>     ```
>     Switch-L3(config)# interface Vlan10
>     Switch-L3(config-if)# ip address 192.168.10.1 255.255.255.240
>     Switch-L3(config-if)# no shutdown
>     Switch-L3(config-if)# exit
>     
>     ```
>     
> 
> ----------

> ### VLAN 20 (Marketing)
> 
> ----------
> 
> **Exemple de Mise en Place d’un VLAN (Marketing) :**
> 
> 1.  Configuration du VLAN sur le switch Layer 2 :
>     
>     ```bash
>     Switch-L2(config)# vlan 20
>     Switch-L2(config-vlan)# name Marketing
>     Switch-L2(config-vlan)# exit
>     
>     ```
>     
> 
> -   Configuration de l’interface VLAN sur le switch Layer 3 :
>     
>     ```bash
>     Switch-L3(config)# interface Vlan20
>     Switch-L3(config-if)# ip address 192.168.20.1 255.255.255.240
>     Switch-L3(config-if)# no shutdown
>     Switch-L3(config-if)# exit
>     
>     ```
>     
> 
> ----------

> ### VLAN 30 (Ressources Humaines)
> 
> ----------
> 
> **Exemple de Mise en Place d’un VLAN (Ressources Humaines) :**
> 
> 1.  Configuration du VLAN sur le switch Layer 2 :
>     
>     ```bash
>     Switch-L2(config)# vlan 30
>     Switch-L2(config-vlan)# name Ressources_Humaines
>     Switch-L2(config-vlan)# exit
>     
>     ```
>     
> 
> -   Configuration de l’interface VLAN sur le switch Layer 3 :
>     
>     ```bash
>     Switch-L3(config)# interface Vlan30
>     Switch-L3(config-if)# ip address 192.168.30.1 255.255.255.248
>     Switch-L3(config-if)# no shutdown
>     Switch-L3(config-if)# exit
>     
>     ```
>     
> 
> ----------

## Activation du DHCP pour Chaque VLAN

Nous avons configuré des serveurs DHCP pour chaque VLAN afin d’attribuer dynamiquement les adresses IP aux périphériques de chaque réseau. Voici comment activer le service DHCP pour chaque VLAN :

> ### VLAN 10 (Développement)
> 
> ----------
> 
> **Exemple d’Activation du DHCP (Développement) :**
> 
> 1.  Configuration du pool DHCP sur le switch Layer 3 :
>     
>     ```bash
>     Switch-L3(config)# ip dhcp pool Developpement
>     Switch-L3(dhcp-config)# network 192.168.10.0 255.255.255.240
>     Switch-L3(dhcp-config)# default-router 192.168.10.1
>     Switch-L3(dhcp-config)# exit
>     
>     ```
>     
> 2.  Activation du service DHCP sur l’interface VLAN :
>     
>     ```bash
>     Switch-L3(config)# interface Vlan10
>     Switch-L3(config-if)# ip dhcp server Developpement
>     Switch-L3(config-if)# exit
>     
>     ```
>     
> 
> ----------

> ### VLAN 20 (Marketing)
> 
> ----------
> 
> **Exemple d’Activation du DHCP (Marketing) :**
> 
> 1.  Configuration du pool DHCP sur le switch Layer 3 :
>     
>     ```bash
>     Switch-L3(config)# ip dhcp pool Marketing
>     Switch-L3(dhcp-config)# network 192.168.20.0 255.255.255.240
>     Switch-L3(dhcp-config)# default-router 192.168.20.1
>     Switch-L3(dhcp-config)# exit
>     
>     ```
>     
> 2.  Activation du service DHCP sur l’interface VLAN :
>     
>     ```bash
>     Switch-L3(config)# interface Vlan20
>     Switch-L3(config-if)# ip dhcp server Marketing
>     Switch-L3(config-if)# exit
>     
>     ```
>     
> 
> ----------

> ### VLAN 30 (Ressources Humaines)
> 
> ----------
> 
> **Exemple d’Activation du DHCP (Ressources Humaines) :**
> 
> 1.  Configuration du pool DHCP sur le switch Layer 3 :
>     
>     ```
>     Switch-L3(config)# ip dhcp pool Ressources_Humaines
>     Switch-L3(dhcp-config)# network 192.168.30.0 255.255.255.248
>     Switch-L3(dhcp-config)# default-router 192.168.30.1
>     Switch-L3(dhcp-config)# exit
>     
>     ```
>     
> 2.  Activation du service DHCP sur l’interface VLAN :
>     
>     ```
>     Switch-L3(config)# interface Vlan30
>     Switch-L3(config-if)# ip dhcp server Ressources_Humaines
>     Switch-L3(config-if)# exit
>     
>     ```
>     
> 
> ----------

## Activation du DHCP pour Chaque VLAN

Nous avons configuré des serveurs DHCP pour chaque VLAN afin d’attribuer dynamiquement les adresses IP aux périphériques de chaque réseau. Voici comment activer le service DHCP pour chaque VLAN :

> ### VLAN 10 (Développement)
> 
> ----------
> 
> **Exemple d’Activation du DHCP (Développement) :**
> 
> 1.  Configuration du pool DHCP sur le switch Layer 3 :
>     
>     ```bash
>     Switch-L3(config)# ip dhcp pool Developpement
>     Switch-L3(dhcp-config)# network 192.168.10.0 255.255.255.240
>     Switch-L3(dhcp-config)# default-router 192.168.10.1
>     Switch-L3(dhcp-config)# exit
>     
>     ```
>     
> 2.  Activation du service DHCP sur l’interface VLAN :
>     
>     ```bash
>     Switch-L3(config)# interface Vlan10
>     Switch-L3(config-if)# ip dhcp server Developpement
>     Switch-L3(config-if)# exit
>     
>     ```
>     
> 
> ----------

> ### VLAN 20 (Marketing)
> 
> ----------
> 
> **Exemple d’Activation du DHCP (Marketing) :**
> 
> 1.  Configuration du pool DHCP sur le switch Layer 3 :
>     
>     ```bash
>     Switch-L3(config)# ip dhcp pool Marketing
>     Switch-L3(dhcp-config)# network 192.168.20.0 255.255.255.240
>     Switch-L3(dhcp-config)# default-router 192.168.20.1
>     Switch-L3(dhcp-config)# exit
>     
>     ```
>     
> 2.  Activation du service DHCP sur l’interface VLAN :
>     
>     ```bash
>     Switch-L3(config)# interface Vlan20
>     Switch-L3(config-if)# ip dhcp server Marketing
>     Switch-L3(config-if)# exit
>     
>     ```
>     
> 
> ----------

> ### VLAN 30 (Ressources Humaines)
> 
> ----------
> 
> **Exemple d’Activation du DHCP (Ressources Humaines) :**
> 
> 1.  Configuration du pool DHCP sur le switch Layer 3 :
>     
>     ```
>     Switch-L3(config)# ip dhcp pool Ressources_Humaines
>     Switch-L3(dhcp-config)# network 192.168.30.0 255.255.255.248
>     Switch-L3(dhcp-config)# default-router 192.168.30.1
>     Switch-L3(dhcp-config)# exit
>     
>     ```
>     
> 2.  Activation du service DHCP sur l’interface VLAN :
>     
>     ```
>     Switch-L3(config)# interface Vlan30
>     Switch-L3(config-if)# ip dhcp server Ressources_Humaines
>     Switch-L3(config-if)# exit
>     
>     ```
>     
> 
> ----------

## Activation de HSRP pour Chaque Switch L3

Nous avons configuré HSRP (Hot Standby Router Protocol) pour assurer la redondance et la haute disponibilité entre nos deux switches Layer 3. Voici comment activer HSRP pour chaque switch L3 :

> ### Switch L3 Prioritaire
> 
> ----------
> 
> **Exemple d’Activation de HSRP (Switch L3 Prioritaire) :**
> 
> 1.  Configuration de HSRP sur l’interface VLAN 10 (Développement) :
>     
>     ```bash
>     Switch-L3-Prioritaire(config)# interface Vlan10
>     Switch-L3-Prioritaire(config-if)# standby 10 ip 192.168.10.1
>     Switch-L3-Prioritaire(config-if)# standby 10 priority 110
>     Switch-L3-Prioritaire(config-if)# standby 10 preempt
>     Switch-L3-Prioritaire(config-if)# exit
>     
>     ```
>     
> 2.  Configuration de HSRP sur l’interface VLAN 20 (Marketing) :
>     
>     ```bash
>     Switch-L3-Prioritaire(config)# interface Vlan20
>     Switch-L3-Prioritaire(config-if)# standby 20 ip 192.168.20.1
>     Switch-L3-Prioritaire(config-if)# standby 20 priority 110
>     Switch-L3-Prioritaire(config-if)# standby 20 preempt
>     Switch-L3-Prioritaire(config-if)# exit
>     
>     ```
>     
> 3.  Configuration de HSRP sur l’interface VLAN 30 (Ressources Humaines) :
>     
>     ```bash
>     Switch-L3-Prioritaire(config)# interface Vlan30
>     Switch-L3-Prioritaire(config-if)# standby 30 ip 192.168.30.1
>     Switch-L3-Prioritaire(config-if)# standby 30 priority 110
>     Switch-L3-Prioritaire(config-if)# standby 30 preempt
>     Switch-L3-Prioritaire(config-if)# exit
>     
>     ```
>     
> 
> ----------

> ### Switch L3 Secondaire
> 
> ----------
> 
> **Exemple d’Activation de HSRP (Switch L3 Secondaire) :**
> 
> 1.  Configuration de HSRP sur l’interface VLAN 10 (Développement) :
>     
>     ```bash
>     Switch-L3-Secondaire(config)# interface Vlan10
>     Switch-L3-Secondaire(config-if)# standby 10 ip 192.168.10.1
>     Switch-L3-Secondaire(config-if)# standby 10 priority 100
>     Switch-L3-Secondaire(config-if)# exit
>     
>     ```
>     
> 2.  Configuration de HSRP sur l’interface VLAN 20 (Marketing) :
>     
>     ```bash
>     Switch-L3-Secondaire(config)# interface Vlan20
>     Switch-L3-Secondaire(config-if)# standby 20 ip 192.168.20.1
>     Switch-L3-Secondaire(config-if)# standby 20 priority 100
>     Switch-L3-Secondaire(config-if)# exit
>     
>     ```
>     
> 3.  Configuration de HSRP sur l’interface VLAN 30 (Ressources Humaines) :
>     
>     ```bash
>     Switch-L3-Secondaire(config)# interface Vlan30
>     Switch-L3-Secondaire(config-if)# standby 30 ip 192.168.30.1
>     Switch-L3-Secondaire(config-if)# standby 30 priority 100
>     Switch-L3-Secondaire(config-if)# exit
>     
>     ```
>     
> 
> ----------

## Élaboration des Règles ACL pour Chaque Switch L3

La mise en place de règles d’accès (ACL - Access Control Lists) est cruciale pour garantir la sécurité et la confidentialité des données au sein de chaque VLAN. Dans cette section, nous allons définir des règles ACL spécifiques pour chaque switch L3 afin de contrôler le trafic entre les différents départements, tout en permettant une communication autorisée.

> ### Switch L3 Prioritaire
> 
> ----------
> 
> #### ACL pour le VLAN 10 (Développement)
> 
> ```
> ip access-list standard VLAN-10-ACL
> permit ip 192.168.10.0 0.0.0.15 any
> permit ip 192.168.20.0 0.0.0.15 any
> permit ip 192.168.30.0 0.0.0.7 any
> deny ip any any
> 
> ```
> 
> ----------
> 
> #### ACL pour le VLAN 20 (Marketing)
> 
> ```bash
> ip access-list standard VLAN-20-ACL
> permit ip 192.168.20.0 0.0.0.15 any
> permit ip 192.168.10.0 0.0.0.15 any
> permit ip 192.168.30.0 0.0.0.7 any
> deny ip any any
> 
> ```
> 
> ----------
> 
> #### ACL pour le VLAN 30 (Ressources Humaines)
> 
> ```bash
> ip access-list standard VLAN-30-ACL
> permit ip 192.168.30.0 0.0.0.7 any
> permit ip 192.168.10.0 0.0.0.15 any
> permit ip 192.168.20.0 0.0.0.15 any
> deny ip any any
> 
> ```
> 
> ----------

> ### Switch L3 Secondaire
> 
> ----------
> 
> #### ACL pour le VLAN 10 (Développement)
> 
> ```bash
> ip access-list standard VLAN-10-ACL
> permit ip 192.168.10.0 0.0.0.15 any
> permit ip 192.168.20.0 0.0.0.15 any
> permit ip 192.168.30.0 0.0.0.7 any
> deny ip any any
> 
> ```
> 
> ----------
> 
> #### ACL pour le VLAN 20 (Marketing)
> 
> ```bash
> ip access-list standard VLAN-20-ACL
> permit ip 192.168.20.0 0.0.0.15 any
> permit ip 192.168.10.0 0.0.0.15 any
> permit ip 192.168.30.0 0.0.0.7 any
> deny ip any any
> 
> ```
> 
> #### ACL pour le VLAN 30 (Ressources Humaines)
> 
> ```bash
> ip access-list standard VLAN-30-ACL
> permit ip 192.168.30.0 0.0.0.7 any
> permit ip 192.168.10.0 0.0.0.15 any
> permit ip 192.168.20.0 0.0.0.15 any
> deny ip any any
> 
> ```
> 
> Ces règles ACL permettent la communication entre tous les VLANs tout en maintenant une isolation logique entre les départements. Elles contribuent ainsi à renforcer la sécurité et la confidentialité des données au sein de l’infrastructure réseau. Les ACLs sont configurées de manière symétrique sur les deux switches L3 pour garantir la redondance et la haute disponibilité.
> 
> ----------

## Bénéfices Attendus

> -   Amélioration des Performances : La segmentation du réseau réduit la charge de diffusion, améliorant ainsi les performances globales.
> -   Sécurité Renforcée : Chaque département est isolé et contrôle le trafic grâce à des pare-feu, renforçant la sécurité des données sensibles.
> -   Gestion Efficace du Trafic : Le routage inter-VLAN facilite la communication entre les départements tout en permettant une gestion efficace du trafic.
> -   Haute Disponibilité : La redondance des équipements assure une disponibilité élevée du réseau, minimisant les interruptions de service.
> 
> ----------

## Propositions d’Amélioration

Nous recommandons d’examiner les possibilités suivantes pour améliorer davantage la sécurité et la gestion du réseau :

> -   Mise en place de règles ACL plus spécifiques pour restreindre davantage la communication entre les départements.
> -   Surveillance continue du trafic réseau pour détecter et prévenir les menaces potentielles.
> -   Implémentation de solutions de sauvegarde et de reprise sur sinistre pour garantir la continuité des activités en cas de catastrophe.
> 
> ----------

## Conclusion

> Le projet d’infrastructure réseau a permis de répondre aux besoins de segmentation, d’isolation, de routage inter-VLAN, de redondance et de sécurité de l’entreprise Eyes4-IT. La nouvelle architecture renforce la performance, la sécurité et la gestion du trafic, tout en offrant une haute disponibilité pour les opérations quotidiennes.
> 
> ----------
