# Arduino-projekt-1
Første arduino projekt 
af August, Emilie og Sebastian

# Problemformulering 
I vores klasseværelse er der ofte tung luft, meget støj og en kvælende temperatur. Dette forringer vores læringsevne ret markant, da det er svært at koncentrere sig og holde fokus når omgivelserne er dårlige. Vi vil derfor gerne undersøge luftkvaliteten, lydniveauet, temperaturen og mængden af fugt ved hjælp af en Arduino. Vi vil gerne undersøge sammenhængen mellem de forskellige faktorer, om der er forskel i pauserne vs. timerne (og dermed om faktorerne påvirkes markant af ændringer i lokalet som fx. mængden af mennesket og om vinduet er åbent) og om vi eventuelt kommer over nogle grænseværdier, som vi derefter kan forbedre enten selv eller ved at få lavet markante ændringer i klasselokalet.

# Flowchart 
Link til flowchart lavet i Miro ↓

https://miro.com/welcomeonboard/MHlqSzBMOTVPWXNoTlNaUFZjUjZzQ0VDeG5VWFA1b2R2UDF1SW1yS3JMaGE4M25BcWZmSGE5Q0xIcFp1WitxdGIvb1VlblY4WlVMWlZtclFnN1l4NTZSTVhMdVhWaGxTMnhZSjNDWkFFa09QRmE1ZmdBVElQZWhsT3hwMlgzbnNNakdSWkpBejJWRjJhRnhhb1UwcS9BPT0hdjE=?share_link_id=627217930046 

# Kode
Arduino 
```cpp
//luft kval måler
#define SDA_PORT PORTD
#define SDA_PIN 2
#define SCL_PORT PORTD
#define SCL_PIN 3
#define I2C_SLOWMODE 1
#define DHTPIN 4
#define DHTTYPE DHT22

#include <SoftI2CMaster.h>
#include <Wire.h>
#include "Adafruit_SGP30.h"
#include "DHT.h"

Adafruit_SGP30 sgp;
unsigned long startTid;
DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(9600);
  dht.begin();
  while (!Serial) { delay(10); }

  i2c_init();

  if (!sgp.begin()) {
    Serial.println("FEJL: SGP30 ikke fundet! Tjek D2/D3 tilslutning.");
    while (1);
  }

  startTid = millis();
  Serial.println("Tid_sek,eCO2_ppm,TVOC_ppb,Status_nr,Status_tekst,dB,C°,RL");
}

void loop() {
  if (!sgp.IAQmeasure()) {
    delay(1000);
    return;
  }

  float tid = (millis() - startTid) / 1000.0;
  float h = dht.readHumidity();
  float t = dht.readTemperature();

  // Tjek om DHT-aflæsning lykkedes
  if (isnan(h) || isnan(t)) {
    Serial.println("FEJL: Kunne ikke læse fra DHT-sensor!");
    delay(1000);
    return;
  }

  int eco2 = sgp.eCO2;
  int tvoc = sgp.TVOC;

  int statusNr;
  String statusTekst;

  if (eco2 < 800) {
    statusNr = 1; statusTekst = "God";
  } else if (eco2 < 1500) {
    statusNr = 2; statusTekst = "Moderat";
  } else {
    statusNr = 3; statusTekst = "Darlig";
  }

  int dB = 1; // stadig placeholder

  Serial.print(tid, 1);       Serial.print(",");
  Serial.print(eco2);         Serial.print(",");
  Serial.print(tvoc);         Serial.print(",");
  Serial.print(statusNr);     Serial.print(",");
  Serial.print(statusTekst);  Serial.print(",");
  Serial.print(dB);           Serial.print(",");
  Serial.print(t, 1);         Serial.print(",");  // <-- rigtig temperatur
  Serial.println(h, 1);                           // <-- rigtig luftfugtighed

  delay(1000);
}```
Python 
```Py
import serial
import time
import os, sys
from datetime import datetime
from openpyxl import load_workbook

COM_PORT  = "COM4"
BAUD_RATE = 9600
pathname = os.path.abspath(os.path.dirname(sys.argv[0]))
EXCEL_FIL = "SGP30_Data.xlsx"
EXCEL_FULL_PATH = os.path.join(pathname,EXCEL_FIL)
GEM_HVERT = 10

def main():
    print("SGP30 -> Excel Logger")

    print(f"Åbner: {EXCEL_FULL_PATH}")
    if not os.path.exists(EXCEL_FULL_PATH):
        print(f"Kunne ikke finde '{EXCEL_FULL_PATH}'")
        input("Tryk Enter...")
        return

    print(f"Forbinder til {COM_PORT}...")
    try:
        ser = serial.Serial(COM_PORT, BAUD_RATE, timeout=2)
        time.sleep(2)
        print("Forbundet! Koerer... tryk Ctrl+C for at stoppe\n")
    except serial.SerialException:
        print(f"Kunne ikke forbinde til {COM_PORT}")
        input("Tryk Enter...")
        return

    wb = load_workbook(EXCEL_FULL_PATH)
    ws = wb["Data"]

    naeste_gem = time.time() + GEM_HVERT
    naeste_row = 2

    try:
        while True:
            line = ser.readline().decode("utf-8", errors="ignore").strip()	
            print(f"DEBUG: '{line}'")
            if not line or "Tidspunkt" in line or "FEJL" in line:
                continue
            parts = line.split(",")
            if len(parts) < 8:
                continue

            try:
                eco2      = int(parts[1])
                tvoc      = int(parts[2])
                status_nr = int(parts[3])
                status    = parts[4].strip()
                dB        = int(parts[5])
                temp      = float(parts[6])
                humidity  = float(parts[7])
            except ValueError:
                continue

            tidspunkt = datetime.now().strftime("%H:%M:%S")

            ws.cell(row=naeste_row, column=1, value=tidspunkt)
            ws.cell(row=naeste_row, column=2, value=eco2)
            ws.cell(row=naeste_row, column=3, value=tvoc)
            ws.cell(row=naeste_row, column=4, value=status_nr)
            ws.cell(row=naeste_row, column=5, value=status)
            ws.cell(row=naeste_row, column=6, value=dB)
            ws.cell(row=naeste_row, column=7, value=temp)
            ws.cell(row=naeste_row, column=8, value=humidity)
            naeste_row += 1

            print(f"  {tidspunkt} | eCO2: {eco2:>5} ppm | TVOC: {tvoc:>4} ppb | dB: {dB:>4} | Temp: {temp:>3}C | Fugt: {humidity:>5.1f}% | {status}")

            if time.time() >= naeste_gem:
                wb.save(EXCEL_FULL_PATH)
                print(f"  [GEMT] {datetime.now().strftime('%H:%M:%S')}")
                naeste_gem = time.time() + GEM_HVERT

    except KeyboardInterrupt:
        print("\nStopper...")
    finally:
        wb.save(EXCEL_FULL_PATH)
        ser.close()
        print("Faerdig! Aaben SGP30_Data.xlsx og tjek Data fanen.")
        input("Tryk Enter...")

if __name__ == "__main__":
    main()```
# Logbog for emnet
## Brainstorm 06-03-2026

Typer af sensorere vi kan bruge: afstand, infrarød, motion sensor, fugt, temperatur, luft kvalitet, gas, menneske radar, touch, vibration/kollision/bevægelse, støv, afstand, ultralyd, vægt, magnet, fugt, encoders, lyd, kraft, puls  

Vi vil gerne lave noget til klasseværelset. 

### Første ide: 

Klasseværelses måler, en måler der måler alt der påvirker et klasseværelse, så som luftkvalitet, temperatur, fugt, lyd, gas, måske mængde mennesker (er der for mange mennesker etc.).

### Anden ide: 

Hvordan påvirker dårlig luft dem i klasseværelset? Vi vil måle luftkvalitet og gas og så et par andre parametre, såsom lyd/støj, temperatur, mængde mennesker (går folk væk hvis luften er shit).

### Tjerde ide:

Plante-vander, vander John Newton (vores plante) hver gang jorden bliver for tør.

### Fjerde ide: 

En bil der kan suse rundt i klassen, (nok lidt uden for vores niveau).

### Femte ide: 

Roomba til klassen, tror ikke vi har noget til at suge, men vi kan måle støv og distance til væggen.

### Sjette ide: 

Menneske logger, ved hjælp af menneske radar kunne vi tjekke hvornår folk mødte ind.

### Syvende ide: 

Sidder man ordenligt-sensor

### Ottende ide: 

Flappy bird ud fra lyd/lys eller luft kvalitet.

## Lyskrydsmetoden til ovenstående ideer. 06-03-2026

Første ide, black box over alt i klasse ![](https://img.shields.io/badge/GOD-grøn-green)

Anden ide, stort set første ide ![](https://img.shields.io/badge/GOD-grøn-green)

Tjerde ide, vande newton (andre går med den ide) ![](https://img.shields.io/badge/GOD-grøn-green)

Fjerde ide, en bil der kører rundt i klassen![](https://img.shields.io/badge/MODERAT-gul-yellow)

Femte ide, ligesom bilen men virker som støvsuger, altså en roomba ![](https://img.shields.io/badge/MODERAT-gul-yellow)

Syvende ide, hvorvidt man sidder ordenligt på en stol og om man sidder for lang tid på den ![](https://img.shields.io/badge/DÅRLIG-rød-red)

Ottende ide, flappy bird ud fra en sensor ![](https://img.shields.io/badge/DÅRLIG-rød-red)

## Ideen valgt 06-03-2026

Vi har valgt at køre med den første/anden ide, nu skal vi så finde ud af hvad vi skal bruge.

Sensorne vi gerne vil bruge, vi vil gerne måle luftkval og gasser (eventuelt menneske sensor i døren som tæller hver gang man går ind eller ud) , hvordan de påvirker så meget andet. det andet er støj, temperatur lufttryk, fugt 

så luftkval, gasser og menneske radar

og støj, temperatur, lufttryk og fugt

# Det vi har lånt

- En Arduino med overdel

- Ledning så den kan gå i computeren

- VOC and eCO2 Sensor (SGP30) v1.1

- Grove-LCD RGB Backlight V4.0

- Sound Sensor v1.6

- Temperature&Humidity Sensor Pro v1.3

- MicroPressureSensor

- En lilla dimmelut Mark gav os


## Logger pro. 06-03-2026

Nu vi gerne skrive et kort program i arduino, og vise dataen i logger pro 

starter med at prøve at måle gas med VOC and eCO2 Gas Sensor (SGP30) v1.1 og vise dataen i loggerpro

Vi brugte Claude, her er Claudes forklaring (forfines senere men lige nu skal python virke og det er vigtigere):

Vi har sat en SGP30 gassensor op på vores Arduino Uno, som måler luftkvalitet i form af eCO2 (kuldioxid) og TVOC (flygtige organiske forbindelser) i klasseværelset. Sensoren er tilsluttet via I2C på pin D4 og D5. For at få dataen ind i vores computer har vi skrevet et Python-script, der automatisk læser målingerne fra Arduino og gemmer dem direkte i en Excel-fil med grafer, så vi kan følge med i luftkvaliteten over tid. Scriptet starter automatisk med et enkelt dobbeltklik og kræver ingen manuel indgriben undervejs.

## Problemformulering (12-03-2026)

I vores klasseværelse er der ofte tung luft, meget støj og en kvælende temperatur. Dette forringer vores læringsevne ret markant, da det er svært at koncentrere sig og holde fokus når omgivelserne er dårlige. Vi vil derfor gerne undersøge luftkvaliteten, lydniveauet, temperaturen og mængden af fugt ved hjælp af en Arduino. Vi vil gerne undersøge sammenhængen mellem de forskellige faktorer, om der er forskel i pauserne vs. timerne (og dermed om faktorerne påvirkes markant af ændringer i lokalet som fx. mængden af mennesket og om vinduet er åbent) og om vi eventuelt kommer over nogle grænseværdier, som vi derefter kan forbedre enten selv eller ved at få lavet markante ændringer i klasselokalet.

## flowchart (12-03-2026)
https://miro.com/welcomeonboard/MHlqSzBMOTVPWXNoTlNaUFZjUjZzQ0VDeG5VWFA1b2R2UDF1SW1yS3JMaGE4M25BcWZmSGE5Q0xIcFp1WitxdGIvb1VlblY4WlVMWlZtclFnN1l4NTZSTVhMdVhWaGxTMnhZSjNDWkFFa09QRmE1ZmdBVElQZWhsT3hwMlgzbnNNakdSWkpBejJWRjJhRnhhb1UwcS9BPT0hdjE=?share_link_id=627217930046
Miro
Miro | The Visual Workspace for Innovation
Miro is a visual workspace for innovation where teams manage projects, design products, and build the future together. Join 60M+ users from around the world.
Billede 


kode for i dag (23/3):

#define SDA_PORT PORTD
#define SDA_PIN 2
#define SCL_PORT PORTD
#define SCL_PIN 3
#define I2C_SLOWMODE 1
#define DHTPIN 4
#define DHTTYPE DHT22
#define lydpin A0

#include <SoftI2CMaster.h>
#include <Wire.h>
#include "Adafruit_SGP30.h"
#include "DHT.h"

Adafruit_SGP30 sgp;
unsigned long startTid;
DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(9600);
  dht.begin();
  while (!Serial) { delay(10); }

  i2c_init();

  if (!sgp.begin()) {
    Serial.println("FEJL: SGP30 ikke fundet! Tjek D2/D3 tilslutning.");
    while (1);
  }

  startTid = millis();
  Serial.println("Tid_sek,eCO2_ppm,TVOC_ppb,Status_nr,Status_tekst,dB,C°,RL");
}

void loop() {
  if (!sgp.IAQmeasure()) {
    delay(1000);
    return;
  }

  float tid = (millis() - startTid) / 1000.0;
  float h = dht.readHumidity();
  float t = dht.readTemperature();

  // Tjek om DHT-aflæsning lykkedes
  if (isnan(h) || isnan(t)) {
    Serial.println("FEJL: Kunne ikke læse fra DHT-sensor!");
    delay(1000);
    return;
  }

  int eco2 = sgp.eCO2;
  int tvoc = sgp.TVOC;
  int statusNr;
  int dB = analogRead(lydpin);

//long sum = 0;
//for(int i=0; i<32; i++)
//{
//    sum += analogRead(lydpin);
//}
//sum = sum / 32;

  String statusTekst;

  if (eco2 < 800) {
    statusNr = 1; statusTekst = "God";
  } else if (eco2 < 1500) {
    statusNr = 2; statusTekst = "Moderat";
  } else {
    statusNr = 3; statusTekst = "Darlig";
  }


  Serial.print(tid, 1);       Serial.print(",");
  Serial.print(eco2);         Serial.print(",");
  Serial.print(tvoc);         Serial.print(",");
  Serial.print(statusNr);     Serial.print(",");
  Serial.print(statusTekst);  Serial.print(",");
  Serial.print(dB);           Serial.print(",");
  Serial.print(t, 1);         Serial.print(",");  
  Serial.println(h, 1);                           

  delay(1000);
}

Udover ovenstående er vi nået langt. Gangen før var Sebastian fraværende, så vi brugte lang tid på at få downloadet de nødvendige programmer og udvidelser på Emilies computer. I dag har August og Emilie brugt tid på at tilslutte en lyd-sensor, rette i koden og forsøge at kalibre den, sådan at vi får værdierne i dB (pt giver den bare rå ACD data, som er en måling af elektriske impulser). Derudover har vi i fællesskab fået Excel til at fungere sådan, at vi kan se den opdatere live i stedet for at crashe programmet hver gang den starter op.



