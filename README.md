# Thermo-Indicateur à Servo avec Alarme
## Prototype Robotique — Raspberry Pi Pico / Projet réalisé dans le cadre du cours d'informatique

# Description du projet
**Ce projet consiste à concevoir un système thermique basé sur le Raspberry Pi Pico. Lorsqu'un objet chaud est approché du capteur, le système réagit de deux façons** :
- **Un servomoteur tourne et pointe vers la température correspondante sur un cadran papier gradué (de 0°C à 50°C)**
- **Un buzzer émet une alarme sonore dès que la température dépasse 30°C.**

**L'objectif est de créer un indicateur visuel et sonore simple, utile pour détecter une surchauffe ou la présence d'un objet chaud à proximité.**

# Démarche
1. **Choix du capteur DHT11 pour mesurer la température ambiante**
2. **Idée d'utiliser un servo comme aiguille d'un thermomètre analogique**
3. **Ajout d'un buzzer pour une alerte au-delà du seuil de 30°C**
4. **Calibration de la conversion température -> angle (0°C = 0°, 50°C = 180°)**

| Composant | Fonctionnement | Rôle |
|-----------|-------------|--------------------|
| Raspberry Pi Pico H | petit microcontrôleur qui éxecute le code | `Microcontrôleur principal` |
| Capteur DHT11 | mesure la température de l'air autour de lui | `Mesure de la température` |
| Buzzer | petit haut parleur qui emet un son | `Alarme sonore (≥ 30°C)` |
| Servomoteur | se positionne entr 0 - 180 degré | `Indicateur visuel (aiguille)` |
