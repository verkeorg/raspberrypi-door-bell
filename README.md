# Rasperry Pi ovikello || Raspberry Pi Doorbell

**Read the instruction in [Eglish](https://github.com/verkeorg/raspberrypi-doorbell/blob/master/README_EN.md).**

### Mitä projektiin tarvitaan?

*   [Raspberry Pi -tietokone](http://www.partco.fi/fi/minitietokoneet/raspberry-pi/17493-raspberry-pi3b.html) (tässä projektissa Pi 3 model B)
*   [RasPi -kotelo](http://www.partco.fi/fi/minitietokoneet/raspberry-pi/17496-raspberry-k3.html) (valinnainen)
*   [Ovikellopainike](http://www.partco.fi/fi/saehkoemekaniikka/kytkimet/ovikellokytkimet/7399-kyt-ovi.html?search_query=ovikello&results=7) (mikä tahansa yksinkertainen kaksikontaktinen painike)
*   [Kytkentäjohtoa](http://www.partco.fi/fi/kaapelitjohdot/yksisaeikeiset-kytkentaejohdot/14316-kaa-kj-1x06-pun.html)
*   Kutistemuovia johtojen kotelointiin (vaihtoehtoisesti valmis kaksisäikeinen kytkentäjohto)
*   [Liittimiä](http://www.partco.fi/fi/liittimet/piikkirima-liittimet/harwin-liittimet/15756-harwin-1x1.html)
*   Kaiuttimet (mitkä tahansa kaiuttimet 3.5mm liitännällä)
*   Kuorintapihdit johtojen eristeiden kuorimiseen (saksilla tai muilla vapaavalintaisilla teräaseillakin pärjää, mutta menee hankalaksi)
*   Pihdit liittimien kiinnitykseen johtoihin
*   Ruuvimeisseli ovikellon asennukseen
*   HDMI -johto, näyttö sekä langallinen hiiri ja näppäimistö (ei tarvita asennuksen jälkeen)

### Konseptin hahmottelu

Minkä tahansa uuden ratkaisun hahmottaminen on tietysti hyvä aloittaa käyttäjän tarpeen pohtimisella. Mitä käyttäjä haluaa painaessaan ovikellon nappia? Hyvin suurella todennäköisyydellä käyttäjä haluaa, että ovi avataan.

Muutama asia pitää kuitenkin tapahtua ennen kuin Verken ahkera työntekijä ymmärtää kipittää ovelle. Jonkinlainen äänimerkki on, kuten edellä todettua, aikalailla se "perusmallin" oletus ovikelloissa; RasPi pitäisi siis saada toistamaan ääntä. Tavoitteena oli myös saada - tavalla tai toisella - ovikello juttelemaan Verkeläisten mobiililaitteisiin. Tähän kanavaksi valikoitui käytössä jo oleva sisäinen Telegram -keskustelu. Ovikellosta RasPiin vaaditaan tietysti myös fyysinen yhteys eli pari johtoa. Ennen kuin nappia painettaessa tapahtuu mitään, pitäisi RasPi myös ohjelmoida toimimaan logiikalla "kun nappia painetaan, soita tämä ääni ja lähetä viesti."

Ketjuksi muodostuu siis jotakuinkin **Painike** **->** **RasPi -> Koodi -> Kaiuttimet + Telegram -botti**. Ei kun toteuttamaan!

### RaspBerry Pin valmistelu

Asenna RasPiin mukana tullut muistikortti RasPin ohjeiden mukaisesti. Kytke näyttö hdmi-kaapelilla, usb-näppäimistö ja -hiiri sekä viimeisenä virtalähde. RasPi käynnistyy automaattisesti työpöydälle. Mikäli Linux -pohjaiset käyttöjärjestelmät eivät ole tuttuja, saattaa RasPin mukana toimitettavan RaspBian -käyttöjärjestelmän työpöytä näyttää hieman oudolta, mutta vasemman ylänurkan valikosta löytyvät melkolailla tutuilta paikoiltaan samanlaiset asetukset kuin muistakin käyttöjärjestelmistä. Jos et käytä verkkokaapelia, kytke RasPi langattomaan verkkoon.

Monet Linux -pohjaisen käyttöjärjestelmän toimenpiteet on näppärintä hoitaa tekstipohjaisen komentorivin eli _t__erminaalin_ kautta. Aivan kaikkea ei graafisilla työkaluilla pysty edes tekemään. RasPin mukana toimitettu käyttöjärjestelmä on todennäköisesti vanhentunut, joten käyttöjärjestelmän päivitys on hyvää harjoitusta terminaalin käytölle. Terminaalia käytetään kirjoittamalla komento ja hyväksymällä se rivinvaihdolla (enter). Avaa terminaali ja kirjoita seuraavat komennot:
```
sudo apt-get update sudo apt-get dist-upgrade
```
Ensimmäinen komento päivittää koneelle listan paketeista (ohjelmista) joita on saatavilla, jälkimmäinen päivittää saatavilla olevat päivitetyt paketit. Komentojen rakenne on seuraava:

*   _sudo_ on lyhenne sanoista "superuser" ja "do", eli komento suoritetaan järjestelmänvalvojan oikeuksilla. Mikäli RasPi kysyy käyttäjätunnuksia ja / tai salasanaa, RasPin perustunnukset ovat pi / raspberry.
*   _apt-get_ on ohjelma, jolla linuxissa haetaan asennettavaksi paketteja, kirjastoja ja lisäohjelmia
*   _update_ ja _dist-upgrade_ ovat ohjelmalle (edellä molemmissa _apt-get_) annettavia komentoja. Käytössä olevista komennoista saa usein tietoa valinnalla --help, esimerkiksi siis _apt-get --help_.

Päivittämisessä kestää todennäköisesti jonkin aikaa, joten odottele rauhassa tai valmistele sillä aikaa nappi johtoineen. Kun päivitys on valmis, kannattaa samalla noutaa [python-telegram-bot](https://github.com/python-telegram-bot/python-telegram-bot) jota tarvitaan jotta saadaan ovikello huutelemaan telegramiin.

``` 
sudo pip install python-telegram-bot --upgrade
```

### Napin liittäminen Raspberry Pihin

![](https://www.verke.org/wp-content/uploads/2017/03/20170224_175748-01-300x225.jpeg) 

Ovikellon testipaketti kasassa ilman virtalähdettä.

Vaikka ovikello periaatteessa toimisi vain johdon päät yhdistämällä, otettiin tämän projektin lähtökohdaksi napin asentaminen ovelle. Saatavilla olleessa napissa on kaksi kontaktia, jotka napinpainallus sulkee. Asenna kytkentäjohdot (lyhyillä 20-30 sentin pätkillä lienee helpompi testata) ovikelloon avaamalla ruuvit ja asentamalla kuoritut johdon päät ruuvinkantojen alle. Toiseen päähän johtoa kannattaa asentaa [Harwin -liittimet](http://www.partco.fi/fi/liittimet/piikkirima-liittimet/harwin-liittimet/15756-harwin-1x1.html); koska ostamiemme liittimien muovikuori ei mahdu RasPin kuoren alle, käytin pelkästään metallista sisäpinniä jonka puristin pihdeillä kiinni liitäntäjohtoon.

![](https://www.verke.org/wp-content/uploads/2017/03/20170223_120124-01-169x300.jpeg) 

Raspberry Pi:n GPIO -liitin ja verkosta tulostettu kytkentäopas.

Raspberry Pissä fyysiset laitteet kytketään pääsääntöisesti GPIO -liitäntään ("General purpose in/out", kuvassa). Liittimen kaikilla pinneillä (kultaiset liitännät) on oma tarkoituksensa ja toiminnallisuutensa. Kytkentää helpottamaan kannattaa tulostaa verkosta kytkentäkaavio, joka kertoo mitä mikäkin liitin tekee. Omani tulostin [täältä](https://github.com/splitbrain/rpibplusleaf/blob/master/rpiblusleaf.pdf). Kytkennät tehdään oikosulkujen välttämiseksi RasPin ollessa irti verkkovirrasta.

Päätin kytkeä ovikellon liittimeen GPIO18\. Ovikellon napista lähtevä toinen johto tulee kiinni maaliitäntään (GND). Näin yksinkertaisessa kytkennässä ei ole väliä sillä, kummin päin johdot kytketään; monimutkaisemmissa sovelluksissa asialla on toki merkitystä. Ovikellon napin painallus käytännössä kytkee yhteen GPIO18 -pinnin sekä maan. Koodaamalla (seuraava osio) saadaan ohjattua, mitä silloin tapahtuu; tätä varten kannattaa muistaa tai kirjoittaa ylös, mikä liitäntä on käytössä.

On olennaista huomioida, että osa pinneistä syöttää virtaa (3V / 5V) ja näiden kanssa kannattaa olla suhteellisen tarkkana, jotta ei aiheuta vaurioita RasPille tai laitetta käyttävien tahdistimille. Näppärämpi rakentaa erillisen, fyysisen jännitesuojan virtapiiriin - ja tätä monessa ohjeessa myös suositellaan - mutta tältä osin oikaisin itse hieman.

### Ovikellon ohjelmointi

Jotta RasPi saadaan reagoimaan napinpainallukseen, tarvitaan hieman ohjelmointia. Useimmiten RasPin ohjelmointiin käytetään Python -ohjelmointikieltä. RasPin käyttöjärjestelmän mukana tulee Python 3 -paketti, jota sopii mainiosti tähän tarkoitukseen. Avaa siis editori RasPin ylävalikosta (Programming -> Python 3 (IDLE). Avaa uusi tiedosto editoriin (file -> new) ja pääset muokkaamaan koodia. Tiedosto kannattaa heti tallentaa, esimerkki voi olla vaikkapa nappitesti.py .

Ensin kannattaa kirjoittaa mahdollisimman yksinkertainen koodi, jolla pystyy testaamaan toimiiko napin fyysinen kytkentä RasPiin. Tämän voi tehdä esimerkiksi seuraavalla koodinpätkällä (selitysosa koodin jälkeen):

```python
# Tuodaan tarvittavat Python -kirjastot 
import RPi.GPIO as GPIO from time import sleep

# Määritellään GPIO -pinnit standardiksi 
GPIO.setmode(GPIO.BCM)

# Määritellään pin 18 sisääntuloksi ja kytketään sisäinen vastus 
GPIO.setup(18, GPIO.IN, pull_up_down=GPIO.PUD_UP)

# napinpainalluksen funktio 
while True: input_state = GPIO.input(18) if input_state == False: print ("Nappi toimii!")
```

Kaikki rivit jotka alkavat risuaita -merkillä ovat kommentteja. Kommenteilla voidaan poistaa rivi koodia tilapäisesti käytöstä tai kuten yllä, selittää mitä mikäkin koodirivi tekee. Ylläolevassa koodissa tapahtuu seuraavaa:

Ensin määritellään koodiin tuotavat kirjastot. Kirjastot ovat ikäänkuin valmiita koodinpätkiä ja kirjastojen käytöllä vältytään ohjelmoimasta yleisiä toimintoja uudelleen. Ylläolevassa on tuotu GPIO -liitännän ohjaamiseen tarvittava kirjasto (ja annettu sille lyhyempi "lempinimi" GPIO) sekä tuotu time -kirjastosta osio "sleep", jolla saadaan koodiin tauko. GPIO -liitäntä tulee määritellä, jotta RasPi ymmärtää, minkätyyppisiä asioita liitäntään on kytketty. Tässä GPIO on määritelty standardimuotoon, jolloin se noudattaa aiemmin printtaamaamme kytkentöjen järjestystä. Seuraavassa määritellään, että nastasta 18 (johon kytkimme ovikellon napin, tämä voisi olla mikä tahansa liittimistä) haistellaan sisääntulevaa signaalia. Lisäksi määrittelemme, että nastassa käytetään sisäistä vastusta; koska RasPi tutkailee nastan 18 ja maa (GND) välisen signaalin voimakkuutta, vastus auttaa selkiyttämään mittaustulosta. Yksinkertaistettuna, koska olen yksinkertainen: ilman vastusta signaali olisi koko ajan jotain epämääräistä väliltä 0-1, jolloin napin tilaa (pohjassa vai ei) ei voida luotettavasti lukea ja ovikello soisi jatkuvasti omia aikojaan. Samaan tulokseen päästäisiin asentamalla fyysinen vastus johtoon, mutta se on käytännössä tässä tarpeetonta.

Tässä oleva esimerkkikoodi napin testaukseen käyttää yksinkertaisinta while -luuppia. Koodi purettuna riveittäin:

*   Sillä aikaa kun tätä koodia ajetaan...
*   lue nastan 18 sisääntulevaa signaalia ja
*   jos nastan signaali on epätosi (eli nappi on pohjassa, hämmentävää kyllä)
*   tulosta viesti "Nappi toimii!"

Viimeinen rivi koodista ("Sleep" -toiminto) säästää prosessorin kuormaa. Vaikka kirjoittamamme koodi ei ole kovin raskas, kuormitti ensimmäinen testiversio koodista prosessorin 100% ja hyydytti RasPin kokonaan. Pieni tutkimustyö ja päättely osoitti, että koodia ajettiin niin monta kertaa kuin resurssit antoivat myöten, koska taukoa ei oltu määritelty. Lisäämällä 0.1 sekunnin tauko koodiin prosessorikuorma putosi merkittävästi.

![](https://www.verke.org/wp-content/uploads/2017/03/Nappi-toimii-300x142.png) 

Kytkennät kunnossa!

Mikäli kaikki meni putkeen, pitäisi koodia ajettaessa (Python -editorista valinta "run" tai vaihtoehtoisesti teminaalin puolella komento _python nappitesti.py _) tulostua ruudulle nappia painettaessa teksti "Nappi toimii!". Terminaalissa koodin ajamisen saa keskeytettyä näppäinyhdistelmällä ctrl-c.

### Telegram -botin ohjelmointi

Tämän projektin lähtökohtana oli saada ovikello juttelemaan äänimerkin lisäksi myös chat -keskusteluun. Meillä se on Telegram, johon täytyy valmiiksi luoda botti joka kanavalle huutelee; ovikello voisi tehdä muutakin, kuten [tweetata](http://nodotcom.org/python-twitter-tutorial.html), [lähettää sähköpostia](http://naelshiab.com/tutorial-send-email-python/) tai vaikkapa ottaa [web -kameralla kuvan](https://harizanov.com/2013/07/raspberry-pi-emalsms-doorbell-notifier-picture-of-the-person-ringing-it/). Projektien vaikeustaso vaihtelee, mutta soveltamalla ja kokeilemalla pystyy tekemään lähes mitä tahansa.

Ennen Telegram -ovikellon varsinaista ohjelmointia täytyy vielä valmistella pari asiaa. Äänikirjasto _pygame_ on jo RaspBianiin asennettu automaattisesti, joten siitä ei tarvitse huolehtia. Telegramia varten tarvitaan kuitenkin [botti](https://core.telegram.org/bots) (älykäs ohjelma, joka juttelee Telegram -kanavalla) sekä valitun keskustelun id-tunnus.

Telegram -botti luodaan suoraan telegramissa. Avaa keskustelu @botfather -botille ja luo uusi Telegram -botti [näiden ohjeiden mukaan](https://core.telegram.org/bots#6-botfather). Kirjoita botin valtuutusavain ("Token", muotoa _110201543:AAHdqTcvCH1vGWJxfSeofSAs0K5PALDsaw_  muistiin. Kutsu botti Telegram -kanavalle, johon haluat ovikellon viestittävän. Kun olet kirjoittanut Botille pari viestiä, siirry osoitteeseen https://api.telegram.org/bot<**BotinValtuutusAvain**>/getUpdates ja etsi kenttä joka on muotoa **"chat":{"id":XXXXXXXXX,"title":"KESKUSTELUN_NIMI"**. Kirjoita keskustelun id-numero muistiin.

### Ovikellon ohjelmointi

Nyt voidaan siirtyä ovikellon varsinaiseen ohjelmointiin. Etsi verkosta haluamasi ovikellon ääni ja lataa tiedosto valmiiksi RasPille. Sen jälkeen kirjoita seuraava koodi esimerkiksi tiedostoon Ovikello.py: 
```python
# I/O sekä aikafunktio viiveelle, äänikirjasto ja systeemikomennot 
import RPi.GPIO as GPIO from time import sleep from os import system from pygame import mixer import logging` logging.basicConfig(level=logging.DEBUG, format='%(asctime)s - %(name)s - %(levelname)s - %(message)$ `import telegram bot = telegram.Bot(token='**XXXXXXXXX-SINUN-BOTTISI-TOKEN**')

# GPIO -pinnien määrittely standardiksi 
GPIO.setmode(GPIO.BCM)

# Määritellään pinni 18 sisääntuloksi sekä kytketään sisäinen vastus 
GPIO.setup(18, GPIO.IN, pull_up_down=GPIO.PUD_UP)

# napinpainalluksen funktio try / finally -loopilla` 
`try: while True: input_state = GPIO.input(18) if input_state == False: bot.sendMessage(chat_id=**-XXXXXXXX**, text="Ovella on joku!")` `mixer.init() mixer.music.load("Sounds/ovikello.wav") mixer.music.play() while mixer.music.get_busy() == True: continue` `sleep(1.0)

# 0.1 sekunnin tauko jotta säästetään prosessorin resursseja 
sleep(0.1)
finally: 

# Resetoidaan napin tila ja lähetetään viesti jos ovikello hyytyy 
bot.sendMessage(chat_id=**-XXXXXXXX**, text="Ovikello on pois päältä!") GPIO.cleanup()

```

Ylläolevasta koodista muutamia huomioita: Koodiin mukaan tuotavien kirjastojen määrä on ensimmäisestä esimerkistä kasvanut. Kirjastoa mixer (joka on osa "pygame" -kirjastoa) tarvitaan äänen toistamiseen ovikellon kaiuttimista. System, logging sekä telegram -kirjastoja taas tarvitaan telegram -botin käskyttämiseen. Mikäli python-telegram-bot -sovellusta ei ole tämän ohjeen ensimmäisessä vaiheessa asennettu, pysähtyy koodin ajaminen ensimmäiseen yritykseen ajaa telegram -kirjaston koodia.

Rivi joka alkaa "_bot = telegram.Bot"_ määrittelee, mitä bottia ovikello käskyttää. Tätä varten tarvitset aiemmassa vaiheessa haetun valtuutusavaimen. Kopioi valtuutusavaimesi token=' ' -kohtaan, mutta ole huolellinen; koodi on tarkka siitä että lainausmerkit (') ovat paikoillaan!

Ensimmäisen esimerkkikoodin yksinkertainen while -looppi on vaihtunut _try-while_ -loopiksi. Näin siksi, että koodiin haluttiin toiminto, joka ilmoittaa siitä että koodin suorittaminen on päättynyt. _finally:_ -komennon jälkeinen koodi siis ajetaan vasta kun _try-while_ -looppi keskeytyy syystä tai toisesta.

Ensimmäinen osa koodista on tuttu: "_sillä aikaa kun tätä koodia ajetaan, jos nappi on pohjassa suorita tämä_". Nyt suoritettava asia on vain muuttunut "Nappi toimii!" -viestin tulostamisesta siihen, että telegram-bottia käsketään lähettämään keskusteluun viesti "Ovella on joku!". Chat_id -muuttujan arvoksi sinun täytyy vaihtaa oman chattisi id, jonka aiemmin kirjoitit ylös. Teksti voi luonnollisesti olla mikä tahansa.

Toisessa palikassa toistetaan ääni RasPiin liitetyistä kaiuttimista. Ensimmäinen rivi kytkee mikseriohjelman pälle, toinen lataa valitun äänen muistiin, kolmas soittaa ääntä ja neljäs estää mikserin käynnistämisen uudelleen äänen toistuessa.

Viimeinen, _sleep(1.0) _-rivi lisää ovikelloon sekunnin mittaisen viiveen. Jonkinlainen viive kannattaa ehdottomasti olla koodissa; tällä kertaa ei säästetä prosessorin kuormaa vaan estetään tuplapainallukset. Yksinkertaisen ovikellokytkimen virtapiiri kun ei sulkeudu lähes koskaan täsmällisesti, vaan "melkein kiinni" -kohdassa kontakteja tulee useampi ja ilman viivettä ovikello soi moneen kertaan. Viive voi tietysti olla pidempikin - vaikka 15 sekuntia - jos haluaa estää hätäisimpien tai pilanpäiten ovikelloa soittavien yritykset häiritä toimiston feng shuita.

Koodin viimeinen lisäys pyrkii siihen, että RasPi ilmoittaisi siitä, jos ovikellon koodi jostain syystä lakkaa toimimasta. Sinänsä koodi toimii tälläisenään, mutta varauksin; mikäli koodin pysäyttää tai se pysähtyy, botti ilmoittaa kyllä kiltisti, että ovikello on pois päältä. Koodi ei kuitenkaan pysty ottamaan huomioon tilannetta, jossa RasPista katkeaa virta (koska koodi ei ehdi ilmoittamaan häiriöstä) tai verkkoyhteyden puutteesta (jolle voisi toki rakentaa oman äänimerkkinsä niin halutessaan).

## Viimeistelyä

![](https://www.verke.org/wp-content/uploads/2017/03/20170227_184154-01-300x300.jpeg) 

Ovikello paikoillaan

Kun kaikki on asennettu lopullisille paikoilleen (pidemmät kaapelit suojattu ja vedetty ovenpieleen asti, RasPille löytynyt sopiva paikka jne.) kannattaa RasPi koteloida. Kuten edellä todettu, Harwin -liittimet eivät mahdu koteloon kuorineen, mutta ainakin [tällä kotelolla](http://www.partco.fi/fi/minitietokoneet/raspberry-pi/17496-raspberry-k3.html) kansi mahtuu sulkeutumaan ongelmitta pelkkiä pinnejä käytettäessä. Johdot kannattaa vetää ulos sopivasta välistä huomioiden, etteivät ne joudu puristuksiin. Koska RasPia on hankala siirtää johdoissa, omaan ovikelloon olisi tarkoitus rakentaa kylkeen liitin, jolla napin johdotuksesta saa irroitettavan. Ajatuksena on käyttää [hyppylankoja](http://www.partco.fi/fi/protoilu/hyppylangat/16050-jmp-nn25-sin.html) sekä [liitinrimaa](http://www.partco.fi/fi/liittimet/piikkirima-liittimet/piikkirimat-tuumarasterilla/2616-pr-2x50-u-r105.html), jolla saa nastat tuotua kotelon ulkopuolelle. Kytkentä on toki muuten sama. Jää nähtäväksi, kuinka siistin liittimen saa toteutettua puukottamalla reiän koteloon ja liimaamalla piikkiriman pätkän paikoilleen..

Siltä varalta, että RasPi jostain syystä käynnistyy uudelleen tai menettää hetkellisesti verkkovirran, kannattaa edellä luotu koodinpätkä virittää käynnistymään automaattisesti RasPin käynnistyessä. Se onnistuu [täältä](http://raspberrypi.stackexchange.com/questions/4123/running-a-python-script-at-startup) löytyviä ohjeita soveltamalla:

Terminaalissa luo uusi tiedosto nimeltä Ovikello.desktop kansioon /home/pi/.config/autostart/ .
```
cd /home/pi/.config/autostart/
```
Kirjoita avautuvassa editorissa tiedostolle seuraava sisältö:
```
[Desktop Entry]
Encoding=UTF-8
Type=Application
Name=Ovikello
Comment=Tämä käynnistää ovikellon automaattisesti RasPin käynnistyessä
Exec=  python /home/pi/Ovikello.py
StartupNotify=false
Terminal=true
Hidden=false
```
Tallenna tiedosto painamalla ctrl-x (poistu) ja vastaamalla myöntävästi kysymykseen tallennetaanko tiedosto. Nyt ovikellon "ohjausohjelman" pitäisi käynnistyä automaattisesti RasPin käynnistyessä.

**Tällä hetkellä oman ovikellon koodissa on vielä yksi vakava puute:** Ovikello ei siedä lainkaan sitä, että RasPi jostain syystä hukkaa verkkoyhteyden. On täysin ymmärrettävää, että Telegram -viestit eivät kulje perille ilman verkkoyhteyttä, mutta jostain syystä myöskään äänet eivät ilman nettiä toistu. Koodiin pitänee rakentaa jonkinlainen vikasieto myös tätä tilannetta varten, tyyppiä "yritä 10 sekuntia postata telegramiin, jos epäonnistuu, palaa alkuun". Äänet voisi myös virittää toistumaan ennen telegram -yhteyttä.

## Mitä projektista jäi käteen?

Projektista jäi tietysti käteen kasa sekalaisia johtoja sekä osia, mutta onneksi jotain muutakin. Ensinnäkin projekti havainnollisti erittäin hyvin itselleni sitä, että erilaisten digitaaliseen nuorisotyöhön liittyvien välineiden ja toimintamallien kokeiluun pitää kertakaikkiaan olla aikaa. Toisekseen uskon että tämä projekti osaltaan innosti minua myös syvempään Maker -kulttuuriin tutustumiseen; on aivan älyttömän siistiä saada lähtökohdaksi "Tässä on RasPi, rakentakaa niistä jotain siistiä" ja sen jälkeen miettiä tarvetta, konseptia, fyysistä rakentelua sekä ohjelmointipuolta. Tässä tuli varmasti opittua jotakin sekä sähkötekniikan että ohjelmoinnin puolelta ja vastaavaa projektia voisi olla hauska lähestyä myös nuorten porukan kanssa. Lämmin suositus vastaaville projekteille!
