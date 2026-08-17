
Du kan nu aslute din SSH l din Regisry server. De gør du ved a skrive exit, og så lukke SSH vindue.
#######################################################################################
Vi skifer tlbage tl den maskine du kører eksemplerne på!! Din worker maskine!
Nu har vi jo oprete e regisry, så må vi hellere å pushe vores image ud l denne server:
docker push crypto
Eksempel oupu:
Åhh.. De virkede ikke. Hvoror nu ikke de? Jo... Sandard vil docker pushe dine images l websiden, og
regisrysiden, docker.io. Men her skal du ørs opretes som bruger or a kunne pushe, og du år kun lov l
a pushe e projek gras. Ellers skal du beale. De gider vi ikke beale or, vi har jo også lige lave vores ege
regisry, hvor vi kan placere vores images. Men de vi ørs skal, er a orælle docker a vi vil pushe l vores
ege Regisry.
De gør vi ved a agge vores image (ligesom en gra kunsner agger sine værker). De gør vi lige:
docker tag crypto:latest ip_Registry_server:5000/crypto:latest
Forklaring:
docker tag: Kommandoen der agger di image.
Crypto:latest : Dete er navne og versionen efer dete orma: Navne:Version (kan også være .eks.
crypo:2.0).
ip_Registry_server:5000/: Dete angiver regisry IP, og hvilken por den skal bruge l
komunikaon.
crypto:latest: Dete er navne, og versionen image gemmes under på Regisry server, efer dete
orma: Navn:Version.
Eksempel Oupu:
Vi må lige kigge lid på hvad der ligger under images nu:
docker image ls -a
Eksempel Oupu:
Her vil man nu se 2 sk. ”images” men læg mærke l a de el, der hedder ”IMAGE ID” er der samme! De
er samme image, men med 2 orskellige navne.
Nu kan vi pushe de image der hedder ip_Registry_server:5000/crypto:latest l vores
regisry server:
docker push ip_Registry_server:5000/crypto:latest
Eksempel Oupu:
ÅÅhh nej.. Der var en ejl igen!
Hvad mon den mener med de? Jo.. den mener bare a de er en regisry server der ikke har nogen htps
reply, men bare rolig! Inden der er nogen der går i panik! Vi kan selvølgelig også løse dete. Vi bliver nød l
a orælle vores worker server a den der Regisry server er OK, og al i orden. Der gør vi ved a løje den
l en speciel l vi lige skal lave:
Førs sarer vi en edior (Der skal indases password, da der skrives ”sudo” oran, indsæ eks ved a
højreklikke i edior):
sudo nano /etc/docker/daemon.json
Filen skal indeholde ølgende (Kopier KUN de remhævede!! Og HUSK a indase IP på DIN regisry
server!!):
################### deamon.json start ##########################
{
"insecure-registries": ["http://ip_Registry_server:5000"]
}
################### daemon.json slut ##########################
Gem len ved a rykke ”Crl+x” -> rykke ”Y” -> Tryk Ener ved lnavn. Og du er lbage l normal prom på
Ubunu.
Ingen eksempel oupu
Så har vi lave en l, men or a de virker bliver vi nød l a gensare docker servicen, du vil blive prompe
or a indase password på din bruger i linux (De kan age lid d a gensare denne service):
sudo systemctl restart docker.service
Eksempel oupu:
Vi må hellere lige konrollere a servicen er sare ordenlig op igen:
systemctl status docker.service
Eksempel oupu:
Der skulle gerne e eller ande sed i oppen så: Acve: acve (running) hvor de sidse er grøn. (Tryk ev.
på CTRL + C or a komme lbage l promp, hvis skærmsørrelse ikke llader a vise al oupu).
Så prøver vi a pushe vores vores image... Igen:
docker push ip_Registry_server:5000/crypto:latest
Eksempel oupu:
Man kan nu se a der bliver overør en hel masse ng l Regisry serveren.
Nu har vi lag noge op på vores Regisry server. Men hvordan nder vi ud a hvad der ligger, eller om der
overhovede ligger noge på den server? De gør vi ved nedensående kommando:
curl -X GET http://ip_Registry_server:5000/v2/_catalog
Forklaring:
Curl: Dete er en linux kommando, der .eks. kan hene websider.
-X Get: Hvad skal vi lave? I dete lælde HENTER vi daa (På engelsk Ge).
http://ip_Registry_server:5000/v2/_catalog: Prookol (htp) og hvor hener vi daa?
Eksempel oupu:
Som man kan se ligger der kun é reposiory, og de er de der hedder crypo... (der kommer ere senere...)
Men hvor mange versioner ligger der så a de der ”crypo” reposiory? Jo de nder vi med denne
kommando. De er aksk kun adressen der er ændre lid, ellers er kommandoen den samme:
curl -X GET http://ip_Registry_server:5000/v2/crypto/tags/list
Eksempel Oupu:
Her kan man se a der kun er e ag under crypo, og de er ”Laes”.
Hvad nu hvis vi sæter endnu e ag på vores image? De er jo nem. De gør vi li.. Sop! Vi gør de lige lid
anderledes, bare or a lære lid også :) Vores image har også e unik ID, dete skal vi lige nde:
docker image ls
Eksempel oupu:
For a nde image ID kigger vi i kolonnen ”IMAGE ID” (lyder logisk.. ikke?) her er image id: 0ab11a57db4,
og de bruger vi lige l a agge vores image med e ny ag:
docker tag IMAGE_ID ip_Registry_server:5000/crypto:2.0
Eksempel Oupu:
Ligesom sids vi aggede e image er der ikke noge a se endnu. Men vi kunne jo prøve a se på hvad der
ligger a images på vores maskine nu:
docker image ls
Eksempel oupu:
Nu ligger der e ny image der har ag 2.0, dog sadig med samme IMAGE ID. De må jo beyde a vi har
lave en eller anden ændring i vores image, og har lave en version 2.0 a vores image. Den ændring må vi
hellere lige sørge or a å pushed ud l vores Regisry (Læg mærke l allersids i kommandoen. Her er
crypto:latest skife l crypto:2.0):
docker push ip_Registry_server:5000/crypto:2.0
Eksempel Oupu:
De og jo ingen d, i orhold l ørse gang? Og der sår en hel masse med ”Layer already exiss”. Hvad
mener den med de? Jo.. or a å orklaringen skal vi lige lid lbage i den, hel lbage l hvor vi lavede
”byggevejledningen”. Den der hed Dockerle. Hver enese kommando i denne l laver e lag (layer) mere.
Dvs. hvis vi ændrer lid i en enkel linje, eller løjer en linje. Så er de kun ændringen der bliver overør og
ev. eferølgende ændringer, som ølge a den ene ændring vi lavede, der vil blive overør. De sparer
plads, og d. Så de enese der havde ændre sig denne gang, de var jo versionen. Så de var kun
versionsnummere der blev overør.
Men hvad ligger så egenlig på vores Regisry nu? Vi kigger lige efer:
curl -X GET http://ip_Registry_server:5000/v2/_catalog
Eksempel oupu:
Hmmmm… Der er sadig kun en pos i vores reposiry... Nåh ja.. vi må hellere lige kigge i vores reposiory
”crypo” og se om der ligger ere versioner:
curl -X GET http://ip_Registry_server:5000/v2/crypto/tags/list
Eksempel oupu:
Her kan vi se a der er komme en version ”2.0” og der er også sadig en version ”laes”.
Nu har vi pushe de hele ud på en server, så nu vil vi slete ALT der ligger på denne vores worker server, så
vi kan se om vi ikke kan å vores image lbage igen..
Før skal vi soppe vores crypo Conainer (JA!.. jeg bruger Kill, de er hurgs, og vi har ikke nogen
daabase..):
docker container kill crypto
Eksempel oupu:
Derefer skal vi slete vores conainere:
docker container rm crypto
Eksempel oupu:
Vi må hellere huske a slete image også, ellers er de snyd. Hvis der er ere conainere, kan de alle sletes
ved a separere de enkele navne med mellemrum, og de kan man også ved images, men de er måske en
ide a nde image navnene, inden vi begynder a slete dem. De gør vi her:
docker image ls -a
Eksempel oupu:
Her er der 3 images, dem sleter vi lige, hvis der er ere, løjes de bare linjen (se eksempel oupu):
docker image rm image1 image2 image3
Eksempel oupu:
Vi må hellere lige sørge or a syseme er hel rydde op, de gør ølgende kommando:
docker system prune
Eksempel oupu:
Der var lige e par eksra ng der skulle sletes, de var aksk alle de ”lag” vi havde lave som var løje de
parren image vi bruge, og vores parren image (php:8.2-pm) blev også slete.
Nu er vi klar l a prøve a Pulle ra vores Regisry.
Slu på Kapiel 4: Pushe image l Regisry.
Kapiel 5: Pulle e image ra Regisry.
Nu har vi jo gem vores image, og vi har slete al på vores maskine. Så mon ikke de er god ide og se om
de image vi har gem, også kan henes lbage? Vi prøver! De gør vi med ølgende kommando:
docker pull crypto
Eksempel oupu:
En Fejl? Igen? Nåååh ja... vi skal huske a specicere vores regisry. Så nu må vi hellere pulle ra vores
regisry:
docker pull ip_Registry_server:5000/crypto
Maskinen arbejder i noge d med a hene, og også a pakke de henede ud. Læg mærke l a maskinen i
eksemple bruger deaul ag der hedder laes. De vil maskinen ald hvis man ikke specicere hvad man vil
hene. Man kan ald efer crypto lave e kolon (:) og skrive version .eks. crypto:2.0 så vil maskinen
hene den specicerede version, hvis den er lsede, ellers kommer der en ejl.
Eksempel oupu:
Vi kan prøve a hene en version der ikke er der, or a se hvordan reakonen er:
docker pull ip_Registry_server:5000/crypto:Findesikke
Eksempel oupu:
Nu skulle vi jo have hene vores image. Skulle vi ikke lige se om den nu også er bleve hene?
docker image ls -a
Eksempel oupu:
YES! Image ligger jo hvor den skal ligge, og de ser ud l a de er vores image.. Men de er jo olle a
hene image, og ikke sare de. Så lad os sare image:
docker run --restart unless-stopped --name crypto -p 80:80 -td ip_Registry_server:5000/crypto
Forklaring:
-td : - beyder a den ikke sender oupu l vores erminal, men l en pseudo erminal, og -d beyder som
beskreve ør deached, alså vores ssh erminal bliver rigive efer sar a conainer.
Eksempel oupu:
Prøv nu a åbne en webbrowser på din pc, og gå igen l ip-adressen på den maskine hvor du har sare din
conainer. I din browser skriver du http://ip_på_worker_makine
Du skulle gener å ølgende side rem igen. Hvis siden kommer rem, er conainer sare igen.:
Slu på Kapiel 5: Pulle e image ra Regisry.
Og de var så de sids.....
STOP! Ven lige lid!
Vi skal lige have rydde lid op inden vi sarer på opgave 2. Vi skal bruge nogen a de daa der ligger på din
maskine endnu ( .eks. biblioeke Sie.). Men vi skal lige have soppe conaineren, slete conaineren, og
erne image. Ellers vil der jo .eks. være en conainer der spærrer or por 80. De med a soppe og slete
har vi jo prøve ør, så lad os gøre de ved a kopiere alle re kommandoer herunder over i vores ssh vindue
på en gang!:
docker kill crypto
docker container rm crypto
docker image rm ip_Registry_server:5000/crypto
Oupu på næse side:
Eksempel oupu:
Vi skal også lige huske en sidse oprydnings kommando:
docker system prune
Eksempel oupu:
Hvad nu?? Den lavede jo ikke noge nu! Bare rolig, de er der ikke noge gal med. Vi har jo pulle dete
image, dvs. compueren har hene al den skulle bruge ra vores Regisry server. Så der lå ikke noge ande
og ”ylde” på vores server, og al, der var pulle ra regisry var lknyte vores image. Se resula ovenover
da vi sletede vores image, alle layers blev slete her!
NU kan jeg så skrive:
Slu på Kapiel 5: Pulle e image ra Regisry.
Og de var så også de sidse i Opgave 1.
