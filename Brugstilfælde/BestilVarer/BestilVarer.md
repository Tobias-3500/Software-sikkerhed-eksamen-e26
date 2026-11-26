# Bestil varer til levering.
Kunde fylder kurven med ønskede varer. Kunden går til checkout, udfylder personlige oplysninger og betalingskort. Kunde placerer ordrer og får en kvittering.

#### Forudsætning:
kunde har oprettet en konto.

#### Aktiver:
Betalingskort værdier, personlige oplysninger, Varer og leveringsadresse. 

#### Misbrugstilfælde:
uautoriseret adgang til andre brugeres ordrer, leveringsoplysninger og kortdata.

Den ondsindede aktør tilgår bestillings- eller ordreoversigten i applikationen og forsøger at fremsøge eller tilgå ordrer, der tilhører andre kunder, for derigennem at få vist fortrolige aktiver i form af ordredetaljer, personlige oplysninger, leveringsadresse samt gemte betalingskortoplysninger.

#### Tilknyttede brugstilfælde:
Nr. 1 (Bestil varer til levering).

#### Forudsætning:
Den ondsindede aktør har adgang til applikationen (f.eks. via en oprettet brugerkonto).

#### Foranstaltning:
Systemet verificerer, at en bruger er logget ind på sin konto (autentifikation), før ordre- og checkout-funktioner kan tilgås, men der mangler en eksplicit og konsekvent kontrol af, at den enkelte ordre eller de tilhørende oplysninger rent faktisk tilhører den pågældende kunde (autorisation). 

#### Bemærkning:
Truslen udgør et direkte brud på fortroligheden (C i CIA-modellen) af kundernes personlige og finansielle aktiver. Hvis adgangskontrollen på ordreniveau ikke håndhæves korrekt, kan enhver bruger med adgang til systemet potentiel tilgå andre kunders historik og oplysninger, hvilket gør dette misbrugstilfælde højt relevant at tage videre til risikovurderingen. 

Analyse af risiko ("Konsekvens"×"Sandsynlighed" )

Risikoanalysen tager udangspunk i en risikomatricen "risikomatrix.png".

Skala for konsekvens (1–5)
   1 – Ubetydeligt
   2 – Begrænset
   3 – Alvorligt
   4 – Farligt
   5 – Katastrofalt
   
Skala for sandsynlighed (1–5)
   1 – Usandsynligt
   2 – Sjældent
   3 – Muligt
   4 – Sandsynligt
   5 – Næsten sikkert
      Risikoen beregnes som konsekvens x sansynliged

Konsekvens: 4 – Farligt (eller 5 – Katastrofalt) 
Begrundelse: Misbrugstilfældet rammer både personlige oplysninger, leveringsoplysninger og betalingskortoplysninger, hvilket kan medføre alvorlige økonomiske, juridiske og omdømmemæssige konsekvenser for forretningen og kunderne.

Sandsynlighed: 3 – Muligt (eller 4 – Sandsynligt) 
Begrundelse: En angriber skal blot have adgang som bruger i systemet, og hvis der mangler ejerskabskontrol på ordreopslag, er det et realistisk scenarie at forsøge. 
Beregnet risiko: 12 HØJ


Evaluering og håndtering af risiko

Evaluering / prioritering: Risikoen vurderes som høj og prioriteres videre frem for at blive behandlet som en accepteret risiko eller fjernet som systemmål. 

Håndtering: Risikoen søges minimeret ved at gå videre til Trusselsmodellering (hvor tekniske trusler identificeres med f.eks. STRIDE) og efterfølgende implementere relevante sikkerhedsforbedringer. 

...usikkerhed, antagelser og manglende viden.
Risikovurderingen er gennemført med et kvalitativt grundlag med fokus på at prioritere misbrugstilfælde frem for præcise risikoværdier. vurderingen bygger på antagelsen om, at enhver autenficieret bruger kan forsøge at tilgå andre kunders ordrer og tilhørende oplysniger. Derudover er der usikkerhed omkring den præcise forretningsmæssige og juridiske omkostning. 



Arkitektur og tillidsgrænser (KISS-niveau)
Komponenter: Browser (kunde/angriber) → API / Webapplikation (ordre- og checkout-logik) → Database (ordrer, kunder, leveringsadresser, betalingskortdata). 

Tillidsgrænse 1 (mellem Browser og API): API'et kontrollerer ikke browseren og må ikke stole på brugerinput eller parametre (såsom ordre-ID eller bruger-ID), der sendes fra browseren. 

Tillidsgrænse 2 (mellem API og Database): Logisk/fysisk afgrænsning mellem applikationslogikken og datalageret, hvor data udveksles og gemmes. 

Relevante STRIDE-trusler og sikkerhedskrav

Information Disclosure (Videregivelse af information) 
Trussel / interaktion: En autentificeret bruger forespørger ordre-, leverings- eller kortdata via API'et, og API'et henter og udleverer data fra databasen uden at kontrollere, om ordren/dataene faktisk tilhører den anmodende bruger. 

Aktiver: Ordredata, personlige oplysninger, leveringsadresse og betalingskortoplysninger. 

Udledt sikkerhedskrav: API'et skal validere ejerskab/rettighed for den aktuelle bruger på enhver forespørgsel om ordre-, leverings- og kortdata, før data returneres. 

A01:2025 Broken Access Control
IDOR
BRUGT TAINT ANALYSIS MED SECURTIY CODE SCAN

Implementering
Med udgangspunkt i den identificerede risiko (12 – Høj) og STRIDE-truslen Information Disclosure (IDOR / manglende ejerskabskontrol på ordreniveau) har vi implementeret server-side ejerskabskontrol i OrderRepository og opdateret CheckoutController, så ordrer kun udleveres, hvis de tilhører den aktuelt autentificerede bruger. Det sikrer, at et ordre-ID sendt fra klienten ikke i sig selv giver adgang til andres data.

Verifikation og begrænsninger
Vi har verificeret ændringen ved at teste applikationen efter implementeringen. Resultaterne dokumenterer, at ejerskabskontrollen blokerer uautoriseret opslag af ordrer. Verifikationen har den begrænsning, at 





