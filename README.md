# Workshop docker - Gluren bij de buren

## Docker machines

Om connectie te maken met de docker machine op jouw kaartje volg de volgende acties

### Download putty en private key

Download een van de onderste versies van putty inbdien je het nog niet op je laptop hebt staan. Download ook de private key.

[putty 64-bit](https://the.earth.li/~sgtatham/putty/latest/w64/putty-64bit-0.68-installer.msi) -
[putty 32-bit](https://the.earth.li/~sgtatham/putty/latest/w32/putty-0.68-installer.msi)

[private key](https://raw.githubusercontent.com/vincentfree/GlurenBijDeBuren/master/workshop_user1.ppk) `rechter muis knop - > opslaan`

### Putty setup

- download putty
- download de "private key"
- in putty vul het volgende in
  - hostname: `ubuntu@<ip-adress>`
  - port: `22`
  - in de "sidebar" ga naar `SSH->Auth` en druk op de `browse` knop
  - Zoek op uw machine naar de private KEY
- U bent nu klaar om verbinding te maken, druk op de open knop onderaan het scherm
- wanneer een vraag in het scherm komt over ssh keys drukt u op `yes`

## Op de docker terminal

Wanneer u ingelogd bent ziet u iets als: `root@ip-182-51-11-251:/#`

Dit is de command line van de docker machine die we gaan gebruiken.
Om te kijken of het werkt gebruik het volgende commando: `docker ps`

Wanneer u het volgende ziet weet u dat uw machine goed werkt: `CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES`

ziet u `Cannot connect to the Docker daemon. Is the docker daemon running on this host?` dan kunt u het volgende invullen: `sudo su` gevolgd door `systemctl start docker`

Nu zijn we echt klaar om te beginnen!

## Docker whale

We gaan eerst een simpele docker container starten waarin een klein script draait die ons een tekst terug gaat geven met wat opmaak in de vorm van het docker logo.

Eerst halen we de docker image op van het internet, dit doen we met het commando `docker pull`.

Docker pull gebruik je om public of private images op te halen.
Dit kan van de standaard docker registry genaamd de docker hub of vanuit een eigen registry die vanuit je eigen machine of zakelijke omgeving gehost kan worden.

Voor dit voorbeeld gaan we gebruik maken van een publiekelijke image van docker zelf.

Vul het volgende in op de commandline

`docker pull docker/whalesay`

`docker run docker/whalesay cowsay hallo uw_naam`

## Docker whale in een Dockerfile

Naast het starten van een container vanaf het internet is het ook mogelijk om een container te beschrijven in een Dockerfile.
We gaan nu een simpele docker file maken, daarna builden en weer met docker run starten.

Eerst maken we een lege file aan met de naam Dockerfile.

`touch Dockerfile`

Nu we een file hebben gaan we hem met het programma `vi` openen door het volgende in te typen: `vi Dockerfile`

Binnen vi druk je op de `i` knop om te mogen wijzigen in de file. Vul het volgende in

```Dockerfile
FROM docker/whalesay
LABEL version 0.0.1
CMD cowsay Hallo <naam>
 ```

Om de wijziging op te slaan vul je het volgende in

- eerst de `Esc` knop
- daarna `:wq` gevolgd door `Enter`

Nu kunnen we een image maken door het volgende commando in te vullen

`docker build -t gbdb/whale:0.0.1 .`

Om de container te starten vul je het volgende commando in `docker run gbdb/whale:0.0.1`

## Webserver in docker met Nginx

Nu gaan we een iets interessanter image maken.
We gaan een website hosten vanuit een docker container.

eerst gaan we het volgend commando uitvoeren

`docker run -p 8000:80 -d --name nginx -v /tmp/websites:/website_files kitematic/hello-world-nginx`

Wanneer je nu in je eigen web browser kijkt op het adres `http://ip_van_het_kaartje:8000/`
dan zie je een webpagina met wat tekst. Dit draait nu in de container die we net gestart hebben.

Nu gaan we op de commandline naar de tmp folder `cd /tmp/websites`

Vanuit hier vullen we `vi index.html` in om de html file aan te passen.
Probeer er kort iets moois van te maken. Het volgende tref je aan in de file
```html
<div style="color: #35393B; margin-top: 100px; text-align: center; font-family: HelveticaNeue-Light, sans-serif;">
  <img src="https://cloud.githubusercontent.com/assets/251292/5254757/a08a277c-7981-11e4-9ec0-d49934859400.png">
  <h2>Voil&agrave;! Your nginx container is running!</h2>
  <div style="color: #838789;">
    <p>To edit files, double click the <strong>website_files</strong> folder in Kitematic and edit the <strong>index.html</strong> file.</p>
  </div>
</div>
```
Na dat je het op de zelfde manier hebt opgeslagen als bij de Dockerfile kan je in de webbrowser zien dat de webpagina verandert wanneer je `f5` gebruikt.

Stop de container door `docker stop nginx` uit te voeren.
Je zou hem nog kunnen verwijderen door `docker rm nginx` uit te voeren.

## Swarm-mode

We hebben nu de basis docker acties uitgevoerd, om te laten zien wat je nog meer met docker kan gaan we orgistratie over meerdere nodes doen.

Voor deze opdracht moeten we samenwerken. Op de briefjes staat een naam en een nummer, wanneer je nog niet samen zit met de mensen met het zelfde nummer ga dan nu samen zitten.

De gene die master op z'n briefje heeft staan kunnen het hier onderstaande commando uitvoeren. Dit is alleen te doen door mensen met de "master" titel, master nodes kunnen nieuwe services (containers die straks over meerdere nodes gedeployed gaan worden) aanmaken.

`docker service create -p 8000:80 --replicas 3 kitematic/hello-world-nginx`

Wat dit commando doet is een service aanmaken dat 3 replicas gaat starten over alle beschikbare nodes, deze containers gaan starten en maken zich bekend op poort `8000`

Nu moet het zo zijn dat op elke machine in jullie groepje een container gestart is, De containers zijn te vinden onder de zelfde url als voorheen.
