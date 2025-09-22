De Basisprincipes: Bayesiaanse Analyse in de Archeologie
De kern van een CQL2-model is de stelling van Bayes, die in de archeologie als volgt wordt vertaald:

Geactualiseerde Kennis (Posterior) = Dateringen (Likelihood) × Archeologische Kennis (Prior)

Dit betekent dat we onze bestaande kennis over een site (de prior beliefs), zoals de stratigrafische volgorde van lagen, combineren met de absolute dateringen (de likelihoods) om tot een verfijndere, geactualiseerde chronologie te komen (de posterior beliefs). Het is een methode die leren van ervaring formaliseert: hypothesen die de data goed voorspellen, worden waarschijnlijker.

2. De Bouwstenen: Kerncommando's van een CQL2-Model
Een CQL2-model wordt opgebouwd uit een reeks commando's die de structuur, de data en de onderlinge relaties definiëren.

A. Structuur en Groepering (De Priors)
Dit zijn de commando's waarmee u uw archeologische kennis in het model vastlegt:

    Plot(): Het overkoepelende commando dat de analyse en visualisatie van het model initieert.

    Sequence(): Dit is het meest fundamentele commando voor het vastleggen van een chronologische volgorde. Het dwingt alle geneste elementen (zoals dateringen of fases) om in de opgegeven volgorde plaats te vinden en is de directe vertaling van een stratigrafische reeks of een Harris Matrix.

    Phase(): Groepeert dateringen of gebeurtenissen waarvan wordt aangenomen dat ze archeologisch gelijktijdig zijn, zoals vondsten uit dezelfde laag. Binnen een Phase is er geen interne chronologische ordening.

    Boundary(): Definieert de begin- en eindgrenzen van een Sequence of Phase. Dit is cruciaal voor het schatten van de start-, eind- en overgangsdata van archeologische fenomenen en om te voorkomen dat dateringen onrealistisch verspreid raken. Het gebruik van boundaries is essentieel, vooral bij veel data met overlappende waarschijnlijkheidsverdelingen.

B. De Data (De Likelihoods)
Dit zijn de commando's voor de meetgegevens:

    R_Date(): Het commando voor een ongekalibreerde radiokoolstofdatering, met de unieke labcode, de ouderdom in jaren BP (Before Present) en de standaarddeviatie.

    Curve(): Een essentieel commando dat specificeert welke kalibratiecurve moet worden gebruikt (bijv. IntCal20 voor het noordelijk halfrond). Dit maakt het model transparant en reproduceerbaar.

    C_Date(): Voor het invoeren van een bekende historische of kalenderdatum, zoals een datering op een munt.

C. Geavanceerde Modellering en Queries
Deze commando's stellen u in staat om complexere relaties te definiëren en specifieke vragen te stellen:

    Outlier_Model() en Outlier(): Een krachtige statistische methode om te gaan met potentiële uitbijters. In plaats van een datering subjectief te verwijderen, geeft een Outlier_Model de software de mogelijkheid om het gewicht van een datering te verminderen als deze niet in het model past. Er zijn specifieke modellen voor verschillende situaties, zoals "General" voor algemeen gebruik en "Charcoal" om rekening te houden met het 'oud-houteffect'.

    D_Sequence() en Gap(): Gebruikt voor "wiggle-matching". Dit modelleert een reeks dateringen van één object (zoals boomringen) met een bekend onderling tijdsverschil (Gap), wat de precisie aanzienlijk kan verhogen, met name op plateaus in de kalibratiecurve.

    Span() en Interval(): Dit zijn queries om de duur van een fase (Span) of het tijdsinterval tussen twee grenzen of gebeurtenissen (Interval) te berekenen.

    Order(): Een query die de waarschijnlijkheid berekent dat de ene gebeurtenis of fase voor de andere plaatsvond.

3. Een Goed Model Bouwen: Best Practices en Validatie
Een CQL2-model is een hypothese die getest moet worden. De bronnen benadrukken een iteratief proces van modelbouw, evaluatie en verfijning.

    Transparantie en Reproduceerbaarheid: Publiceer altijd de volledige CQL2-code, de gebruikte softwareversie (bv. OxCal 4.4) en de kalibratiecurve (bv. IntCal20), zodat anderen uw werk kunnen controleren en reproduceren.

    Model Fit Evalueren: Een model moet statistisch worden gevalideerd. OxCal biedt hiervoor de Agreement Indices (Amodel en Aoverall). Een waarde boven de 60% wordt over het algemeen als acceptabel beschouwd en geeft aan dat de data en het model met elkaar in overeenstemming zijn. Ook moet de convergentie (C) van de MCMC-simulatie hoog zijn (idealiter >95%).

    Wanneer het model niet past: Als de Agreement Index laag is, kan dit wijzen op een uitbijter in de data, maar het kan ook betekenen dat de archeologische aannames (de priors in uw model) onjuist zijn. In plaats van data te verwerpen, moet de archeoloog eerst het model zelf kritisch evalueren. Soms is een eenvoudiger model dat meer van de data kan accommoderen een betere oplossing dan een complex, overgespecificeerd model dat veel data uitsluit.

    Simulatie voor Robuustheid: Voordat men een duur dateringsprogramma opzet, kan de R_Simulate() functie in OxCal gebruikt worden om te onderzoeken hoeveel dateringen nodig zijn om een robuuste chronologie te verkrijgen voor een specifieke archeologische vraag.

Samenvattend is een CQL2-model een formele, expliciete en testbare hypothese over de chronologie van een archeologische site. Het combineert de statistische waarschijnlijkheden van dateringen met de logische structuur van de stratigrafie, wat resulteert in een chronologie die preciezer, betrouwbaarder en archeologisch veelzeggender is dan een losse lijst van gekalibreerde data ooit zou kunnen zijn.

https://c14.arch.ox.ac.uk/databases.html
