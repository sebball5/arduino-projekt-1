# arduino-projekt-1
Første arduino projekt 
af August, Emilie og Sebastian

# brainstorm 06-03-2026

typer af sensorere vi kan bruge: afstand, infrarød, motion sensor, fugt, temperatur, luft kval, gas, menneske radar, touch, vibration/kollision/bevægelse, støv, afstand ultralyd, vægt, magnet, fugt, encoders, lyd, kraft, puls  

vi vil gerne lave noget til klasse værelset. 

### første ide: 

klasse værelse måler, en måler der måler alt der påvirke i et klasseværelse, så som luftkval, temperatur, fugt, lyd , gas, måske mængde mennesker (er der for mange mennesker).

### anden ide: 

hvordan påvirker dårlig luft dem i klasse værelset, vi vil måle luftkval og gas og så et par andre parametre, såsom lyd/støj, temperatur mængde mennesker (går folk væk hvis luften er shit).

### tjerde ide:

plante vander, vander john newton (vores plant) hver gang jorden blir for tør.

### fjerde ide: 

en bil der kan suse rundt i klasse, (nok lidt uden for vores niveau).

### femte ide: 

roomba til klasse, tror ikke vi har noget til at suge, men vi kan måle støv og distance til væggen.

### sjerde ide: 

menneske logger, ved hjælp af menneske radar kunne vi tjekke hvornår folk mødte ind.

### syvende ide: 

sidder man ordenligt

### ottoende ide: 

flappy bird ud fra lyd/lys eller luft kval.

# Lyskrydsmetoden til ovenstående ideer. 06-03-2026

Første ide, black box over alt i klasse ![](https://img.shields.io/badge/GOD-grøn-green)

Anden ide, stort set første ide ![](https://img.shields.io/badge/GOD-grøn-green)

tjerde ide, vande newton (andre går med den ide) ![](https://img.shields.io/badge/GOD-grøn-green)

fjerde ide, en bil der kører rundt i klassen![](https://img.shields.io/badge/MODERAT-gul-yellow)

femte ide, ligesom bilen men virker som støvsuger, altså en roomba ![](https://img.shields.io/badge/MODERAT-gul-yellow)

syvende ide, hvorvidt man sidder ordenligt på en stol og om man sidder for lang tid på den ![](https://img.shields.io/badge/DÅRLIG-rød-red)

ottoende ide, flappy bird ud fra en sensor ![](https://img.shields.io/badge/DÅRLIG-rød-red)

# ideen valgt 06-03-2026

vi har valgt at køre med den første/anden ide, nu skal vi så finde ud af hvad vi skal bruge.

sensorne vi gerne vil bruge, vi vil gerne måle luftkval og gasser (eventuelt menneske sensor i døren som tæller hver gang man går ind eller ud) , hvordan de påvirker så meget andet. det andet er støj, temperatur lufttryk, fugt 

så luftkval, gasser og menneske radar

og støj, temperatur, lufttryk og fugt

# Logger pro era.

nu vi gerne skrive et kort program i arduino, og vise dataen i logger pro 

starter med at prøve at måle gas med VOC and eCO2 Gas Sensor (SGP30) v1.1 og vise dataen i loggerpro

vi brugt claude her er claudes forklaring forfines senere men lige nu skal python virker og det er vigtigter 

Vi har sat en SGP30 gassensor op på vores Arduino Uno, som måler luftkvalitet i form af eCO2 (kuldioxid) og TVOC (flygtige organiske forbindelser) i klasseværelset. Sensoren er tilsluttet via I2C på pin D4 og D5. For at få dataen ind i vores computer har vi skrevet et Python-script, der automatisk læser målingerne fra Arduino og gemmer dem direkte i en Excel-fil med grafer, så vi kan følge med i luftkvaliteten over tid. Scriptet starter automatisk med et enkelt dobbeltklik og kræver ingen manuel indgriben undervejs.
