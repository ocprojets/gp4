# Thermo-Indicateur à Servo avec Alarme
## Prototype Robotique — Raspberry Pi Pico / Projet réalisé dans le cadre du cours d'informatique

# Description du projet
**Ce projet consiste à concevoir un système thermique basé sur le Raspberry Pi Pico. Lorsqu'un objet chaud est approché du capteur, le système réagit de deux façons** :
- **Un servomoteur tourne et pointe vers la température correspondante sur un cadran papier gradué (de 0°C à 50°C)**
- **Un buzzer émet une alarme sonore dès que la température dépasse 30°C.**

**L'objectif est de créer un indicateur visuel et sonore simple, utile pour détecter une surchauffe ou la présence d'un objet chaud à proximité.**

# Démarche du projet
1. **Choix du capteur DHT11 pour mesurer la température ambiante**
2. **Idée d'utiliser un servo comme aiguille d'un thermomètre analogique**
3. **Ajout d'un buzzer pour une alerte au-delà du seuil de 30°C**
4. **Calibration de la conversion température -> angle (0°C = 0°, 50°C = 180°)**

# Liste des composants
| Composant | Fonctionnement | Rôle |
|-----------|-------------|--------------------|
| Raspberry Pi Pico H | petit microcontrôleur qui éxecute le code | `Microcontrôleur principal` |
| Capteur DHT11 | mesure la température de l'air autour de lui | `Mesure de la température` |
| Buzzer | petit haut parleur qui emet un son | `Alarme sonore (≥ 30°C)` |
| Servomoteur | se positionne entr 0 - 180 degré | `Indicateur visuel (aiguille)` |

# Nouveau Composants
- **Capteur de température DHT11** : Le DHT11 est un capteur numérique qui mesure la température et l'humidité de l'air. Il intègre une sonde et un convertisseur qui envoie les données directement en signal numérique au Pico.
- **Caractéristiques techniques** : Plage de température: 0°C à 50°C, Précision: 2°C, Communication: signal numérique 1-Wire, Tension: 3.3V

- **Debo Buzzer Passif P1** : Un buzzer passif est un composant qui produit un son lorsqu'on lui envoie un signal PWM oscillant. Contrairement à un buzzer actif, il a besoin d'une fréquence externe pour sonner — ce qui permet de contrôler le ton du son.
- **Principe techniques** : Le buzzer reçoit un signal PWM sur GPIO14

- **Source** : https://components101.com/sensors/dht11-temperature-sensor
https://www.reichelt.com/de/en/shop/product/developer_boards_-_buzzer_passive-282661

# Schéma de cablage

<img width="666" height="573" alt="schéma de cablage" src="https://github.com/user-attachments/assets/67b1e692-09e4-42fb-a5ac-0c1bd1077e54" />

# Explication du code

