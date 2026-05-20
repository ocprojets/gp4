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
## Structure générale
Le code est divisé en trois parties : l'initialisation des composants, la définition des fonctions, et la boucle principale qui tourne en continu.
## Initialisation des composants
Au démarrage, le programme configure les trois composants :
- Le DHT11 est associé à la broche GP15 via la librairie dht
- Le buzzer est configuré en PWM sur GP14 avec une fréquence de 4000 Hz (son aigu)
- Le servo est configuré en PWM sur GP16 avec une fréquence de 50 Hz (standard pour les servos)
## Fonction set_servo_angle(angle)
Cette fonction convertit un angle (entre 0° et 180°) en signal PWM compréhensible par le servo. Elle commence par sécuriser la valeur reçue pour qu'elle reste dans la plage autorisée, puis calcule la largeur d'impulsion correspondante avec la formule : duty = 1000 + (angle / 180) × 8000 .
Plus l'angle est grand, plus le duty cycle est élevé, et plus le servo tourne vers la droite sur le cadran.
## Fonctions buzzer_on() et buzzer_off()
buzzer_on() envoie un duty cycle très élevé (60 000 sur 65 535) au buzzer pour produire un son fort. buzzer_off() envoie 0 pour couper le son complètement.
## Boucle principale (while True)
C'est le cœur du programme. Il s'exécute en boucle infinie et effectue les actions suivantes à chaque cycle :
- **Attente de 2 secondes** — le DHT11 ne peut faire qu'une mesure toutes les 2 secondes minimum.
- **Mesure de la température** — capteur.measure() déclenche la lecture, capteur.temperature() récupère la valeur.
- **Déplacement du servo** — la température est convertie en angle avec (température / 50) × 180, ce qui fait pointer l'aiguille vers la bonne graduation sur le cadran papier.
- **Déclenchement du buzzer** — si la température est supérieure ou égale à 30°C, le buzzer s'active ; sinon, il reste silencieux.
- **Gestion des erreurs** — si le DHT11 ne répond pas (mauvais câblage, absence de résistance pull-up), une erreur OSError est capturée et un message explicatif s'affiche dans la console.

# Code en entier
# ============================================================
# IMPORT DES LIBRAIRIES
# ============================================================

# Permet de contrôler les broches (Pin) et le PWM (signal modulé)
from machine import Pin, PWM

# Librairie pour utiliser le capteur DHT11
import dht

# Gestion du temps (pause, délais)
import time


# ============================================================
# INITIALISATION DU CAPTEUR DHT11
# ============================================================

# On crée un objet "capteur" connecté sur la broche GP15
# → c’est la broche DATA du DHT11
capteur = dht.DHT11(Pin(15))


# ============================================================
# INITIALISATION DU BUZZER
# ============================================================

# Le buzzer est connecté sur GP14
buzzer = PWM(Pin(14))

# On définit une fréquence élevée (4000 Hz)
# → plus la fréquence est haute, plus le son est aigu
buzzer.freq(4000)


# ============================================================
# INITIALISATION DU SERVO
# ============================================================

# Le servo est connecté sur GP16
servo = PWM(Pin(16))

# Les servos fonctionnent généralement à 50 Hz
servo.freq(50)


# ============================================================
# FONCTION : CONTRÔLER L'ANGLE DU SERVO
# ============================================================

def set_servo_angle(angle):
    """
    Cette fonction permet de positionner le servo
    à un angle donné entre 0° et 180°.
    """

    # Sécurité : on limite l'angle entre 0 et 180
    angle = max(0, min(180, angle))

    # Conversion angle → signal PWM
    # 1000 = position mini
    # 9000 ≈ position max
    duty = int(1000 + (angle / 180) * 8000)

    # On envoie la valeur au servo
    servo.duty_u16(duty)


# ============================================================
# FONCTIONS POUR LE BUZZER
# ============================================================

def buzzer_on():
    """
    Active le buzzer avec une puissance élevée.
    """
    buzzer.duty_u16(60000)  # presque le max (65535)


def buzzer_off():
    """
    Coupe complètement le buzzer.
    """
    buzzer.duty_u16(0)


# ============================================================
# BOUCLE PRINCIPALE
# ============================================================

while True:
    try:
        # ----------------------------------------------------
        # IMPORTANT : le DHT11 est lent
        # → on attend 2 secondes avant chaque lecture
        # ----------------------------------------------------
        time.sleep(2)

        # Demande une mesure au capteur
        capteur.measure()

        # Récupère la température en degrés Celsius
        temperature = capteur.temperature()

        # Affiche la température dans la console
        print("Température :", temperature, "°C")

        # ----------------------------------------------------
        # CONTRÔLE DU SERVO
        # ----------------------------------------------------
        # On transforme la température en angle :
        # 0°C  → 0°
        # 50°C → 180°
        angle = (temperature / 50) * 180

        # On applique l'angle au servo
        set_servo_angle(angle)

        # ----------------------------------------------------
        # CONTRÔLE DU BUZZER (SEUIL = 40°C)
        # ----------------------------------------------------
        if temperature >= 40:
            # Si la température dépasse 40°C :
            # → le buzzer s'active (alerte chaleur)
            buzzer_on()
        else:
            # Sinon → silence
            buzzer_off()

    except OSError as e:
        # Si erreur de lecture du DHT11
        print("Erreur DHT11 :", e)
        print(" Vérifie câblage + résistance pull-up")

    # Petite pause avant la boucle suivante
    time.sleep(1)
