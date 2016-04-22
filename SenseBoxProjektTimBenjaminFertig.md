# **SenseBox BenTim**




### **Ziel:**
Unsere SenseBox beinhaltet drei Sensoren:
Einen UV-Lichtsensor (*VEML6070*), welcher UV-Strahlung in yM/m^2 misst, 
einen Temperatur- and Feuchtigkeitssensor(*HDC1000/1008*), welcher die Temperatur in Grad Celsius und die Luftfeuchtigkeit in Prozent misst und einen Feinstaubsensor (*Grove Dust Sensor*), welcher die Anzahl von Feinstaubpartikeln in Partikeln pro tausendstel Mikrofuß misst. Vorallem der Feinstaubsensor sollte uns interessante Werte herausgeben, da die Station in der Nähe des Münsteraner Innenstadtrings steht.

## **Materialien:**
### **Aus der Sensebox:**
- Genuino Uno
- **WIZnet** Board Model W5500 Ethernet Shield V1.0 
- UEML6070 UV-Light-sensor
- HDC1008 Temperature- and Humiditysensor												

### **Zusätzliche Hardware:**
- Base Shield V2.0
- SHINYEI PPD42 Grove Dust Sensor 
- Breadboard 
- Fibox
- div. Kabel
- Heißkleber & Tesafilm 

## **Setup Beschreibung:**

### **Hardwarekonfiguration:**
Die Basis unserer Sensebox bildet das **Genuino Uno Board**. 
An dieses haben wir das **WIZnet Board Model W5500 Ethernet Shield V1.0** angeschlossen, es ermöglicht uns die Internetverbindung herzustellen. Darauf wurde das **Base Shield V2.0** gesteckt. Letzteres wird für den Anschluss des Feinstaub-Sensors benötigt. Zur Veranschaulichung seht ihr hier den Aufbau der drei Boards.
---
![alt text][ardu]
![alt text] [wiznet]
![alt text] [base]
---


![alt text][Fritzing]
---

Die Sensoren wurden entsprechend der Fritzing-Abbildung angeschlossen, wobei der Anschluss des Feinstaubsensors durch den blauen Sensor dargestellt wird. Zu beachten dabei ist, dass der Feinstaub-Sensor zusammen mit dem mitgelieferten Stecker an den D8 Anschluss gesteckt wird. Der Temperatur-/Feuchtigkeitssensor (HDC1000) und der UVSensor (VEML6070) müssen noch verkabelt werden. Dabei ist zu beachten, das der HDC1000 über 3,3V Anschluss mit Strom versorgt wird, der VEML6070 hingegen wird mit dem 5V Anschluss verbunden. Beide Sensoren nutzen die analogen Anschlüsse A4 und A5.
Das Base Shield V2.o, also das oberste Board müsste dann so aussehen: 
![alt text][boardverkablung]
---
Als nächstes bereiten wir unsere Schutzhülle vor, die Basis dafür bildetet eine Fibox. Wir bohren ein Loch in eine Wand unserer Box.
So können wir einerseits Strom-/Internetkabel aus der Box führen um eine Verbindung zum Stromnetz/ Internetanschluss herzustellen und andererseits die Kabel für Temperatur/Feuchtigkeitssensor und Feinstaubsensor aus der Hülle führen.

![alt text][boxloch]

Da sich in der Box andere Feinstaub- und Temperatur- bzw. Feuchtigkeitswerte ergeben als außerhalb, bedingt durch u.a. Sonneneinstrahlung, Wind etc., ist es wichtig, dass diese beiden Sensoren später außerhalb der Box angebracht sind.
Die Kabel für den UVSensor können in der Box bleiben, da dieser in der Box bleibt.
---
Für den Tempertaur-/Feuchtigkeitssensor bauen wir eine kleines Schutzfach, das verhindert, dass der Sensor durch Sonneneinstrahlung und Wassertropfen manipuliert oder geschädigt wird. Damit dennoch kein hitzestau entsteht, wird dieses Fach zur Belüftung mit Löchern versehen und ist nach unten geöffnet.
Den Sensor haben wir mit Heißkleber in der Box festgeklebt.

1. ![alt text][boxtemp4] 2. ![alt text][boxtemp5] 

`3. ![alt text][boxtemp6]

___

Ein entsprechendes Gehäuse für den Feinstaubsensor muss auch angefertigt werden. Dabei sollte man vorallem darauf achten, dass der Sensor gut belüftet ist und der später aufrecht steht, da er sonst nicht richtig funktioniert.

![alt text][boxdust1] 
![alt text][boxdust2]
---

Der UV-Sensor kann im Fiboxdeckel angebracht werden, da er so dem Licht ausgesetzt ist und vor Witterung geschützt beleibt. Man kann ihn also einfach mit Tesafilm oder Heißkleber von innen an den Deckel der Box Kleben.

![alt text][boxuv]

### **Softwaresketch:**

##### Zusätzliche Bibliotheken: 
Für den Temperatur-/Feuchtigkeitssenor muss vorab noch eine Bibliothek vom Hersteller geladen werden, diese findet ihr hier: [HDC1000/1008 Bibliothek für Arduino](https://github.com/RFgermany/HDC100X_Arduino_Library)


Falls noch nicht bekannt ist wie ihr die Bibliothek in Arduino einfügt findet ihr hier ein recht gutes Tutorial dazu anhand des Temp-/Feuchtigkeitssensors: [Tutorial Temp-/Feuchtigkeitssensor](https://github.com/sensebox/oer/wiki/05_Der_serielle_Datenbus_und_die_Verwendung_von_Software_Bilbiotheken)

Nun folgt der Sofwaresketch, der für das ermitteln der Messwerte und den Upload zuständig ist.
Nach der Anmeldung bei  [OpenSenseMap](http://opensensemap.org/#/) bekommt man einen Code zugesendet, der für den Upload bestimmter Daten codiert. Nun muss man die Sketche die zur Messung der Daten zuständig sind an der richtigen Stelle einfüge, sodass die ermittelten Daten hochgeladen werden. Zu der Registrierung findet ihr im nächsten Inhaltspunkt noch ein paar Informationen.


Zuerst werden die Bibliotheken eingefügt. Die ersten beiden sind bereits im OpenSenseMap-Code vorgeschrieben, die anderen beiden werden für UV- bzw. Temp/Feuchtigkeitssensor gebraucht.



```c++

#include <SPI.h>
#include <Ethernet.h>
#include <Wire.h>
#include <HDC100X.h>

```
Danach kommen einige Zeilen die zu den Sketchen der jeweiligen Sensoren gehören, und hauptsächlich Variablen und Konstanten festlegen.
```c++
// UV-Licht:
#define I2C_ADDR 0x38
  //Integration Time:
  #define IT_1_2 0x0 //1/2T
  #define IT_1   0x1 //1T
  #define IT_2   0x2 //2T
  #define IT_4   0x3 //4T 
  

//Temperatur + Luftfeuchtigkeit
    HDC100X HDC1(0x43);


//Feinstaubsensor:
  int pin = 8;
unsigned long duration;
unsigned long starttime;
unsigned long sampletime_ms = 30000;
unsigned long lowpulseoccupancy = 0;
float ratio = 0;
float concentration = 0;
```

Der folgende Teil ist wieder aus dem OpenSenseMap-Code. Er weißt unter anderem den Sensoren bestimmte ID's zu, diese werden folglich in eurem Code nicht identisch sein.
Interessant für uns ist noch der letzte Absatz. Hier wird eine Variable vom Typ Integer mit dem Namen "postInterval" erzeugt. Diese steht im OpenSenseMap-Code auf 10000, was für ein Uploadintervall von 10s (10000ms) steht. Der Wert muss auf 30000 geändert werden, dass hängt mit dem Messintervall der Feinstaubsensors zusammen, denn dieser kann nur alle 30s einen richtigen Wert liefern.

```c++
//SenseBox ID
#define SENSEBOX_ID "5706283d45fd40c8197460a1"

//Sensor IDs
#define TEMPSENSOR_ID "5706283d45fd40c8197460a6"
#define SENSOR1_ID "5706283d45fd40c8197460a5" // Luftfeuchtigkeit 
#define UVSENSOR_ID "5706283d45fd40c8197460a4"
#define SENSOR2_ID "5706283d45fd40c8197460a3" // Feinstaubkonzentration 

//Ethernet-Parameter
char server[] = "www.opensensemap.org";
byte mac[] = { 0xDE, 0xAD, 0xBE, 0xEF, 0xFE, 0xED };
// Diese IP Adresse nutzen falls DHCP nicht möglich
IPAddress myIP(192, 168, 0, 42);
EthernetClient client;

//Messparameter
int postInterval = 30000; //Uploadintervall in Millisekunden
long oldTime = 0;
```

Nun folgt das Setup, hier müssen wieder ein paar Sachen eingefügt werden, an den Kommentaren seht ihr was zu welchem Sensor gehört und was vom OpenSenseMap-Code stammt. Der OpenSenseMap-Teil prüft und zeigt euch später im Serial von Arduino an, ob das Herstellen der Verbindung mit dem Internet erfolgreich ist.

```c++
void setup()
{

  //Feinstaub:  
   	pinMode(8,INPUT);
   
   
  //UV-Licht:
  	Wire.begin();

 	Wire.beginTransmission(I2C_ADDR);
	Wire.write((IT_1<<2) | 0x02);
	Wire.endTransmission();

  //Temperatur + Feuchtigkeit:
  	HDC1.begin(HDC100X_TEMP_HUMI,HDC100X_14BIT,HDC100X_14BIT,DISABLE);  
  
  
  //OpenSenseMap-Code
  Serial.begin(9600); 
  Serial.print("Starting network...");
  //Ethernet Verbindung mit DHCP ausführen..
  if (Ethernet.begin(mac) == 0) 
  {
    Serial.println("DHCP failed!");
    //Falls DHCP fehltschlägt, mit manueller IP versuchen
    Ethernet.begin(mac, myIP);
  }
  Serial.println("done!");
  delay(1000);
  Serial.println("Starting loop.");

  
}
```

Jetzt folgt der Loop-Teil, der teil der immer wieder ausgeführt wird, da wir ja kontinuierlich Daten messen wollen und nicht nur ein einziges Mal. Da wir das Uploadintervall auf 30s gestellt haben (s.o.) ist kontinuierlich hier auch als alle 30s zu verstehen.

Man sieht nun den Teil des Codes der aus den gemessenen Daten, verwendbare Werte macht, diese also so umformt, dass wir Werte in einer bestimmten Einheit bekommen.
Die postFloatValue()-Methode läd dann den erechneten Wert, der als Variable zwischengespeichert ist hoch. Man gibt ihm als Parameter die Variable in der der Messwert gespeichert ist, die gewünschte Anzahl an Stellen hinter dem Komma bei diesem Wert und die zugehörige SensorID, die wir weiter oben zugewiesen bekommen haben.

```
void loop()
{
  
  duration = pulseIn(pin, LOW);
  lowpulseoccupancy = lowpulseoccupancy+duration;
  //Upload der Daten mit konstanter Frequenz
  if (millis() - oldTime >= postInterval)
  {
    oldTime = millis();

  //Feinstaub:
    ratio = lowpulseoccupancy/(sampletime_ms);  // Integer percentage 0=>100
    concentration = 1.1*pow(ratio,3)-3.8*pow(ratio,2)+520*ratio+0.62; // using spec sheet curve
    lowpulseoccupancy = 0
    postFloatValue(concentration, 2, SENSOR2_ID);
  
    // UV-Licht:
    byte msb=0, lsb=0;
    float uv;

    Wire.requestFrom(I2C_ADDR+1, 1); //MSB
    delay(1);
    if(Wire.available())
      msb = Wire.read();
  
    Wire.requestFrom(I2C_ADDR+0, 1); //LSB
    delay(1);
    if(Wire.available())
      lsb = Wire.read();
   
    uv = (msb<<8) | lsb;
    uv = uv * 5.625;
  
    postFloatValue(uv, 3, UVSENSOR_ID);
  
    //temp
    float temperature = HDC1.getTemp();
    postFloatValue(temperature, 2, TEMPSENSOR_ID);
    
    //humidity
    float humidity = HDC1.getHumi();
    postFloatValue(humidity, 2, SENSOR1_ID);
  }
}
```

Dieser Teil stammt wieder aus dem OpenSenseMap-Code und prüft und zeigt euch im Serial an ob das Hochladen erfolgreich war oder nicht.
```
void postFloatValue(float measurement, int digits, String sensorId)
{ 
  //Float zu String konvertieren
  char obs[10]; 
  dtostrf(measurement, 5, digits, obs);
  //Json erstellen
  String jsonValue = "{\"value\":"; 
  jsonValue += obs; 
  jsonValue += "}";  
  //Mit OSeM Server verbinden und POST Operation durchführen
  Serial.println("-------------------------------------"); 
  Serial.print("Connectingto OSeM Server..."); 
  if (client.connect(server, 8000)) 
  {
    Serial.println("connected!");
    Serial.println("-------------------------------------");     
    //HTTP Header aufbauen
    client.print("POST /boxes/");client.print(SENSEBOX_ID);client.print("/");client.print(sensorId);client.println(" HTTP/1.1");
    client.println("Host: www.opensensemap.org"); 
    client.println("Content-Type: application/json"); 
    client.println("Connection: close");  
    client.print("Content-Length: ");client.println(jsonValue.length()); 
    client.println(); 
    //Daten senden
    client.println(jsonValue);
  }else 
  {
    Serial.println("failed!");
    Serial.println("-------------------------------------"); 
  }
  //Antwort von Server im seriellen Monitor anzeigen
  waitForServerResponse();
}

void waitForServerResponse()
{ 
  //Ankommende Bytes ausgeben
  boolean repeat = true; 
  do{ 
    if (client.available()) 
    { 
      char c = client.read();
      Serial.print(c); 
    } 
    //Verbindung beenden 
    if (!client.connected()) 
    {
      Serial.println();
      Serial.println("--------------"); 
      Serial.println("Disconnecting.");
      Serial.println("--------------"); 
      client.stop(); 
      repeat = false; 
    } 
  }while (repeat);
}
 
```

## **OpenSenseMap Registrierung:**
Zur Anmeldung auf [OpenSensemap.org](http://opensensemap.org/#/) müsst ihr auf den Link klicken oder die Adresse manuell eingeben und dann auf den Punkt Regestrierung gehen. Dort müsst ihr Name, Email-Adresse, Ort der Station sowie euere angebrachten Sensoren und deren Messeinheiten angeben. Daraufhin bekommt ihr eine Mail mit dem Code für euren Upload, diesen müsst ihr dann wie oben gezeigt modifizieren. 

### **Stationsaufbau:**
Nun müsst ihr eure Station nur noch aufbauen, den Sketch rüberladen und mit dem Internet und einer Stromversorgung verbinden und schon kann es losgehen. 

Hier ein Bild unserer Station. Wir haben sie auf unserer Fensterbank angebracht. Diese ist nach Westen ausgerichter und in unmittelbarer Nähe des Coesfelder Kreuzes in Münster gelegen und sollte somit vorallem im Hinblick auf den Feinstaub interessante Werte ausgeben! Standort und Werte könnt ihr [hier](http://opensensemap.org/#/explore/5706283d45fd40c8197460a1) einsehen.

![alt text][boxstation]


### **Kontakt:**
**Tim Richter, Benjamin Karic**

**t_rich14@uni-muenster.de**
**b_kari02@uni-muenster.de**

*22.04.2016*

[ardu]: https://github.com/bkari02/Sensebox-Project-Dust-Temperature-Humidity-UV/blob/Sensebox-Project/IMG_20160422_105943%20-%203.jpg?raw=true

[fritzing]: https://github.com/bkari02/Sensebox-Project-Dust-Temperature-Humidity-UV/blob/Sensebox-Project/Fritzing%20Schma%20PNG.png?raw=true

[wiznet]: https://github.com/bkari02/Sensebox-Project-Dust-Temperature-Humidity-UV/blob/Sensebox-Project/-3.jpg?raw=true

[base]: https://github.com/bkari02/Sensebox-Project-Dust-Temperature-Humidity-UV/blob/Sensebox-Project/Board%203.jpg?raw=true

[boardverkablung]: https://github.com/bkari02/Sensebox-Project-Dust-Temperature-Humidity-UV/blob/Sensebox-Project/Board%20Verkablung.jpg?raw=true

[boxloch]: https://github.com/bkari02/Sensebox-Project-Dust-Temperature-Humidity-UV/blob/Sensebox-Project/Box%20Loch.jpg?raw=true

[boxtemp4]: https://github.com/bkari02/Sensebox-Project-Dust-Temperature-Humidity-UV/blob/Sensebox-Project/BoxTemp4.jpg?raw=true

[boxtemp5]: https://github.com/bkari02/Sensebox-Project-Dust-Temperature-Humidity-UV/blob/Sensebox-Project/BoxTemp5.jpg?raw=true

[boxtemp6]: https://github.com/bkari02/Sensebox-Project-Dust-Temperature-Humidity-UV/blob/Sensebox-Project/BoxTemp6.jpg?raw=true

[boxdust1]: https://github.com/bkari02/Sensebox-Project-Dust-Temperature-Humidity-UV/blob/Sensebox-Project/BoxDust1.jpg?raw=true

[boxdust2]: https://github.com/bkari02/Sensebox-Project-Dust-Temperature-Humidity-UV/blob/Sensebox-Project/BoxDust2.jpg?raw=true

[boxuv]: https://github.com/bkari02/Sensebox-Project-Dust-Temperature-Humidity-UV/blob/Sensebox-Project/BoxUV.jpg?raw=true

[boxstation]: https://github.com/bkari02/Sensebox-Project-Dust-Temperature-Humidity-UV/blob/Sensebox-Project/BoxStation.jpg?raw=true