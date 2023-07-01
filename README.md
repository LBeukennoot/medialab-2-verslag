# 🦆 Ontwikkelend

> Je ontwikkelt binnen een korte periode in een ontwikkelomgeving de kern van het concept zodat deze testbaar en demonstreerbaar is voor alle stakeholders (prototyping). Je ontwikkelt agile: je ontwikkelt vanaf het begin zodat er direct onderdelen zichtbaar worden. Je experimenteert met code/met technologie.

## <mark style="color:green;">Hardware</mark>

Thuis had ik al een raspberry liggen, wat het een lage drempel maakte om ermee te experimenteren. Voordat ik bezig ben geweest met de raspberry had ik nog geen kennis, behalve dat ik 'm ooit aan heb kunnen zetten.

Om er zeker van te zijn ben ik bezig geweest met researchen naar wat de raspberry zou kunnen. Het is een klein computertje dus in theorie zou hij netzoals een normale server (mits deze aan het internet is gekoppeld) HTTP requests kunnen afhandelen.

Naast dat ik thuis een raspberry had liggen, had ik ook een 'shell' liggen, welke je aan de raspberry zou kunnen sluiten om bepaalde functionaliteiten makkelijker te maken. Ik had de GrovePi liggen, welke een koppeling tussen code en verschillende add-ons zoals sensoren en LED's. Helaas door, vermoed ik, een outdated systeem kon ik het niet aan de praat krijgen en heb ik deze richting dus moeten laten vallen. Als backup plan had ik om LED's direct via de GPIO's (de programmeerbare outputs van de raspberry) aan te sluiten. In theorie zou je hier ook een speaker op kunnen aansluiten, maar door gebrek aan tijd heb ik dit niet meer kunnen testen.

<div>

<figure><img src=".gitbook/assets/image (46).png" alt="" width="563"><figcaption><p>Raspberry pi met 'hat' GrovePi</p></figcaption></figure>

 

<figure><img src=".gitbook/assets/image (9).png" alt="" width="563"><figcaption><p>Raspberry pi GPIO pins</p></figcaption></figure>

</div>

Op internet heb ik een simpel stukje python code gevonden die deze GPIO's aanstuurt. Eerder had ik al code gevonden om HTTP requests af te handelen en heb deze twee stukjes code gecombineerd, zodat een request een GPIO pin zou aansturen. De code is hieronder te zien:

{% code fullWidth="false" %}
```python
...
    # Code triggering the code to turn on the light
    def do_GET(self):
        html = '''
        <html>
        <body>
        <p>this webpage turn on and then turn off LED after 2 seconds</p>
        <script src="http://code.jquery.com/jquery-1.12.4.min.js"></script>
        <script>
          function setLED()
            {{
              $.ajax({
              type: "POST",
              url: "http://%s:%s",
              data :"on",
              success: function(response) {
                alert("LED triggered")
              }
            });
          }}
          setLED();
        </script>
        </body>
        </html>
        '''
        self.do_HEAD()
        html=html % (self.server.server_name, self.server.server_port)
        self.wfile.write(html.encode("utf-8"))

    # Code checking body and changing output value
    def do_POST(self):
        content_length = int(self.headers['Content-Length'])
        post_data = self.rfile.read(content_length).decode("utf-8")
        if post_data == "on":
            GPIO.setmode(GPIO.BCM)
            GPIO.setwarnings(False)
            GPIO.setup(18, GPIO.OUT)
            GPIO.output(18, GPIO.HIGH)
            time.sleep(2)
            GPIO.output(18, GPIO.LOW)
        elif post_data == "blue":
            GPIO.setmode(GPIO.BCM)
            GPIO.setwarnings(False)
            GPIO.setup(17, GPIO.OUT)
            GPIO.output(17, GPIO.HIGH)
            time.sleep(2)
            GPIO.output(17, GPIO.LOW)
        elif post_data == "go":
            GPIO.setmode(GPIO.BCM)
            GPIO.setwarnings(False)
            GPIO.setup(4, GPIO.OUT)
            GPIO.output(4, GPIO.HIGH)
            time.sleep(2)
            GPIO.output(4, GPIO.LOW)
        self._redirect('/')

# full code: https://github.com/Medialab-Bedrijventerreinen/Micro-Controller/blob/main/web_gpio.py
...
```
{% endcode %}

Later heb ik toegevoegd dat, afhankelijk van wat er in de body van de request staat, er een andere GPIO pin wordt geactiveerd. Door dit te doen ben ik er ook achter gekomen dat bepaalde pins niet bedoeld zijn om lampjes op aan te sluiten, bijvoorbeeld pin 14 heeft het label 'UART'. Nadat ik de documentatie heb gelezen snapte ik nogsteeds niet wat het verschil is, maar het lijkt er op neer te komen dat dit een pin is waarmee je data kan versturen. Ik had niet genoeg tijd om tijdens het project me hier verder in te verdiepen wat ook niet nodig was, dus ik zal dat bewaren voor in de vakantie :upside\_down:.

En dan natuurlijk het resultaat: het proof-of-concept is behaald! Zodra je een knop in de app indrukt, wordt er een request naar het IP van de raspberry verstuurd, welke controleert wat er in de body staat, en op basis daarvan de juiste pin een output geeft waardoor er een LED oplicht.

<figure><img src=".gitbook/assets/image (4).png" alt="" width="375"><figcaption><p>De lampjes geconnect aan de raspberry</p></figcaption></figure>

## <mark style="color:green;">Wireframe</mark>

Tijdens het project zijn er verschillende wireframes en prototypes gemaakt, waar ik me niet volledig in kon vinden. Om die reden heb ik zelf een wireframe opgezet welke mijn idee representeert. In de ervaring die ik tot nu toe heb opgedaan ben ik erachter gekomen dat functionaliteit moet uitstralen wat het doet, dit heb ik geprobeerd toe te passen:

<div>

<figure><img src=".gitbook/assets/Home pagina (1).png" alt="" width="293"><figcaption><p>Wireframe van teamgenoten</p></figcaption></figure>

 

<figure><img src=".gitbook/assets/Home pagina.png" alt="" width="293"><figcaption><p>Mijn wireframe</p></figcaption></figure>

</div>

Door de knoppen die zorgen voor functionaliteiten in de stoel daadwerkelijk bij de stoel te plaatsen maakt het veel inzichtelijker. Het zou helemaal perfect zijn als een lamp of een speaker daadwerkelijk op de afbeelding van de stoel te zien zijn zodat het nog duidelijk wordt. Hetzelfde geldt voor de muziekspeler die eronder staat, als je door de vorm en icons duidelijk kan maken wat het doet, is het veel intuïtiever voor de gebruiker. Uit het onderzoek wat door de CMD'ers later is uitgevoerd blijkt ook dat het de gebruikers uitnodigd om de functionaliteiten uit te proberen

## <mark style="color:green;">React app</mark>

Zoals in het kopje 'hardware' ook al beschreven is: vanuit de app wordt er een request gestuurd naar de raspberry door op een knop te drukken, wat ervoor zorgt dat er een lampje gaat branden. Naast dat dit werkt is dit ook een hele goede manier, op deze manier wordt de code van software en hardware volledig gescheiden. De app hoeft geen taken voor de hardware uit te voeren.

<figure><img src=".gitbook/assets/ezgif.com-video-to-gif.gif" alt=""><figcaption><p>Demonstratie van de koppeling tussen de app en de raspberry</p></figcaption></figure>

Omdat het vond dat de hardware kant karig was heb ik mijn wireframe ook in de app gemaakt. Ik had al eerder met react en react native gewerkt dus het scheelde dat ik het zo kon oppakken.&#x20;

<figure><img src=".gitbook/assets/image (33).png" alt=""><figcaption><p>Tijdens het ontwikkelen (<a href="https://github.com/Medialab-Bedrijventerreinen/React-App/tree/main/ChairApp">Github</a>)</p></figcaption></figure>

## <mark style="color:green;">Reflectie</mark>

<table data-card-size="large" data-column-title-hidden data-view="cards"><thead><tr><th></th><th></th><th data-hidden></th></tr></thead><tbody><tr><td><strong>Wat ging er goed?</strong></td><td>Ik heb vooraf goed nagedacht over de raspberry zodat, als mogelijkheden niet zouden werken ik een backup plan had om er voor te zorgen dat ik alsnog iets had om op te leveren.</td><td></td></tr><tr><td><strong>Wat kon er beter?</strong></td><td>Ik had minder tijd aan de GrovePi kunnen besteden zodat ik later meer tijd over had om meer te kunnen doen met de raspberry, zoals een speaker aansluiten of live rgb kleuren instellen.</td><td></td></tr><tr><td><strong>Wat zou ik in het vervolg anders doen?</strong></td><td>Mijn wireframe zelf maken in plaats van erop vertrouwen dat iemand anders mijn idee zou visualiseren, zodat ik mijn vindingen tijdens de prototype party al kon valideren door te testen.</td><td></td></tr><tr><td><strong>Wat heb ik geleerd?</strong></td><td>Met hardware en software omgaan en de koppeling ertussen. In het eerste jaar hebben we het topje van de ijsberg behandeld, maar met de raspberry is heel veel meer mogelijk.</td><td></td></tr></tbody></table>

