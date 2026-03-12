# Arduino-projekt-1
Første arduino projekt 
af August, Emilie og Sebastian

# Brainstorm 06-03-2026

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

# Lyskrydsmetoden til ovenstående ideer. 06-03-2026

Første ide, black box over alt i klasse ![](https://img.shields.io/badge/GOD-grøn-green)

Anden ide, stort set første ide ![](https://img.shields.io/badge/GOD-grøn-green)

Tjerde ide, vande newton (andre går med den ide) ![](https://img.shields.io/badge/GOD-grøn-green)

Fjerde ide, en bil der kører rundt i klassen![](https://img.shields.io/badge/MODERAT-gul-yellow)

Femte ide, ligesom bilen men virker som støvsuger, altså en roomba ![](https://img.shields.io/badge/MODERAT-gul-yellow)

Syvende ide, hvorvidt man sidder ordenligt på en stol og om man sidder for lang tid på den ![](https://img.shields.io/badge/DÅRLIG-rød-red)

Ottende ide, flappy bird ud fra en sensor ![](https://img.shields.io/badge/DÅRLIG-rød-red)

# Ideen valgt 06-03-2026

Vi har valgt at køre med den første/anden ide, nu skal vi så finde ud af hvad vi skal bruge.

Sensorne vi gerne vil bruge, vi vil gerne måle luftkval og gasser (eventuelt menneske sensor i døren som tæller hver gang man går ind eller ud) , hvordan de påvirker så meget andet. det andet er støj, temperatur lufttryk, fugt 

så luftkval, gasser og menneske radar

og støj, temperatur, lufttryk og fugt

# Logger pro era.

Nu vi gerne skrive et kort program i arduino, og vise dataen i logger pro 

starter med at prøve at måle gas med VOC and eCO2 Gas Sensor (SGP30) v1.1 og vise dataen i loggerpro

Vi brugte Claude, her er Claudes forklaring (forfines senere men lige nu skal python virke og det er vigtigere):

Vi har sat en SGP30 gassensor op på vores Arduino Uno, som måler luftkvalitet i form af eCO2 (kuldioxid) og TVOC (flygtige organiske forbindelser) i klasseværelset. Sensoren er tilsluttet via I2C på pin D4 og D5. For at få dataen ind i vores computer har vi skrevet et Python-script, der automatisk læser målingerne fra Arduino og gemmer dem direkte i en Excel-fil med grafer, så vi kan følge med i luftkvaliteten over tid. Scriptet starter automatisk med et enkelt dobbeltklik og kræver ingen manuel indgriben undervejs.
