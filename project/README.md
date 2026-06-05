# Les ressources du projet

## Code Arduino

Voici le code complet de notre robot Skarki. Il gère le déplacement, 
le contrôle via RemoteXY en Bluetooth, le mode autonome grâce au 
capteur ultrason, ainsi que la gestion des bras articulés.

// ============================================================
// RemoteXY + ESP32 BLE
// ============================================================

#define REMOTEXY_MODE__ESP32CORE_BLE

#include <BLEDevice.h>
#include <ESP32Servo.h>
#include <RemoteXY.h>
#include "notes.h"

// ============================================================
// RemoteXY connection settings
// ============================================================

#define REMOTEXY_BLUETOOTH_NAME "robot otto g2"

// ============================================================
// RemoteXY GUI configuration
// ============================================================

#pragma pack(push, 1)

uint8_t const PROGMEM RemoteXY_CONF_PROGMEM[] =
{
  255,4,0,0,0,93,0,19,0,0,0,114,111,98,111,116,32,111,116,116,
  111,32,103,50,0,26,1,106,200,1,1,3,0,10,7,38,47,47,50,134,
  35,131,66,82,65,83,32,79,78,0,33,66,82,65,83,32,79,70,70,0,
  5,18,100,72,72,32,1,27,31,10,57,15,36,36,50,134,35,146,67,79,
  85,82,83,69,32,79,78,0,33,67,79,85,82,83,69,32,79,70,70,0
};

struct
{
  // Input variables
  uint8_t pushSwitch_01;
  int8_t joystick_01_x;
  int8_t joystick_01_y;
  uint8_t pushSwitch_02;

  // Other variable
  uint8_t connect_flag;

} RemoteXY;

#pragma pack(pop)

// ============================================================
// SERVOS OTTO
// ============================================================

Servo piedDroit;
Servo piedGauche;
Servo jambeDroite;
Servo jambeGauche;
Servo brasGauche;
Servo brasDroit;

// Pins servos
const int pinPD = D9;
const int pinPG = D7;
const int pinJD = D10;
const int pinJG = D8;
const int pinBG = D3;
const int pinBD = D6;

// Ultrason
const int trig_pin = D2;
const int echo_pin = D1;

// Buzzer
const int buzzer_pin = D0;

int melody[] = {NOTE_G5, NOTE_C6, NOTE_G6};
int duree[]  = {120, 150, 200};
int delai[]  = {180, 220, 300};

// ============================================================
// POSITION REPOS
// ============================================================

void positionRepos()
{
  piedDroit.write(90);
  piedGauche.write(90);
  jambeDroite.write(90);
  jambeGauche.write(100);

  brasDroit.write(90);
  brasGauche.write(65);
}

// ============================================================
// OSCILLATION SERVOS
// dir = 1  -> avance
// dir = -1 -> recul
// ============================================================

void oscillerServos(int steps, int T_ms, int dir)
{
  const int AMP_JAMBE = 30;
  const int AMP_PIED  = 28;

  const int CENTRE_JD = 90;
  const int CENTRE_JG = 98;

  const int CENTRE_PD = 92;
  const int CENTRE_PG = 90;

  const int OFF_PD = 4;
  const int OFF_PG = -4;

  double phase_pieds = dir * (PI / 2.0);

  unsigned long t_start = millis();
  unsigned long duree_totale = (unsigned long)(steps) * T_ms;

  while (millis() - t_start < duree_totale)
  {
    RemoteXY_Handler();

    double t = (double)(millis() - t_start);
    double angle = 2.0 * PI * t / (double)T_ms;

    // Jambes
    int jd = CENTRE_JD + (int)(AMP_JAMBE * sin(angle));
    int jg = CENTRE_JG - (int)(AMP_JAMBE * sin(angle));

    // Pieds
    int pd = CENTRE_PD + OFF_PD +
             (int)(AMP_PIED * sin(angle + phase_pieds));

    int pg = CENTRE_PG + OFF_PG -
             (int)(AMP_PIED * sin(angle + phase_pieds));

    jambeDroite.write(constrain(jd, 0, 180));
    jambeGauche.write(constrain(jg, 0, 180));

    piedDroit.write(constrain(pd, 0, 180));
    piedGauche.write(constrain(pg, 0, 180));

    delay(10);
  }
}

// ============================================================
// ROTATIONS
// ============================================================

void droite()
{
  jambeGauche.write(92);
  piedDroit.write(90);
  jambeDroite.write(92);
  piedGauche.write(90);

  RemoteXY_delay(100);

  piedDroit.write(55);
  RemoteXY_delay(100);

  jambeGauche.write(34);
  RemoteXY_delay(100);

  piedDroit.write(90);
  RemoteXY_delay(100);

  jambeGauche.write(94);
  RemoteXY_delay(100);
}

void gauche()
{
  jambeGauche.write(90);
  piedDroit.write(90);
  jambeDroite.write(90);
  piedGauche.write(90);

  RemoteXY_delay(100);

  piedGauche.write(125);
  RemoteXY_delay(100);

  jambeDroite.write(150);
  RemoteXY_delay(100);

  piedGauche.write(90);
  RemoteXY_delay(100);

  jambeDroite.write(90);
  RemoteXY_delay(100);
}

// ============================================================
// MOUVEMENTS
// ============================================================

void avance()
{
  oscillerServos(2, 650, -1);
}

void recul()
{
  oscillerServos(2, 650, 1);
}

// ============================================================
// CAPTEUR DISTANCE
// ============================================================

float lireDistance()
{
  digitalWrite(trig_pin, LOW);
  delayMicroseconds(2);

  digitalWrite(trig_pin, HIGH);
  delayMicroseconds(10);

  digitalWrite(trig_pin, LOW);

  long duration = pulseIn(echo_pin, HIGH, 30000);

  float distance = (duration * 0.0343) / 2;

  return (distance == 0) ? 100 : distance;
}

// ============================================================
// SETUP
// ============================================================

void setup()
{
  RemoteXY_Init();

  Serial.begin(115200);

  pinMode(trig_pin, OUTPUT);
  pinMode(echo_pin, INPUT);

  // Mélodie démarrage
  for (int i = 0; i <= 2; i++)
  {
    tone(buzzer_pin, melody[i], duree[i]);
    delay(delai[i]);
  }

  noTone(buzzer_pin);

  // Attache servos
  piedDroit.attach(pinPD);
  piedGauche.attach(pinPG);

  jambeDroite.attach(pinJD);
  jambeGauche.attach(pinJG);

  brasDroit.attach(pinBD);
  brasGauche.attach(pinBG);

  positionRepos();

  delay(1000);
}

// ============================================================
// MOUVEMENT BRAS
// ============================================================

void mouvementBras()
{
  static int pos = 50;
  static int sens = 1;

  brasDroit.write(180 - pos);
  brasGauche.write(pos);

  pos += sens * 4;

  if (pos >= 130)
  {
    pos = 130;
    sens = -1;
  }

  if (pos <= 50)
  {
    pos = 50;
    sens = 1;
  }
}

// ============================================================
// LOOP
// ============================================================

void loop()
{
  RemoteXY_Handler();

  float distance = lireDistance();

  if (RemoteXY.pushSwitch_02)
  {
    avance();
  }
  else
  {
    positionRepos();
  }

  // Bras ON/OFF
  if (RemoteXY.pushSwitch_01)
  {
    mouvementBras();
  }
  else
  {
    brasDroit.write(180 - 150);
    brasGauche.write(150);
  }

  /*
  if (distance < 15)
  {
    positionRepos();
    return;
  }
  */

  // Commandes joystick
  if (RemoteXY.joystick_01_y >= 50)
  {
    avance();
  }
  else if (RemoteXY.joystick_01_y <= -50)
  {
    recul();
  }
  else if (RemoteXY.joystick_01_x >= 50)
  {
    droite();
  }
  else if (RemoteXY.joystick_01_x <= -50)
  {
    gauche();
  }
  else
  {
    piedDroit.write(90);
    piedGauche.write(90);
    jambeDroite.write(90);
    jambeGauche.write(100);
  }

  delay(20);
}

Maxence DUHAMEL 
Étudiant UniLaSalle Amiens École d’ingénieurs Énergie & Numérique,
1ère année (I1)

signatureImage

## Fichier Onshape

Le modèle 3D complet de Skarki est disponible sur Onshape :
[Voir le modèle 3D](https://cad.onshape.com/documents/612c47d5092cb63e2e20956a/w/2da54d9e692497291a6d6850/e/bc57a69ff337fc3d13f8cbc7)
