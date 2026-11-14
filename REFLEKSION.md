# Refleksion – Figma til kode

**Gruppemedlemmer:** Katrine Bruhn og Vanest Jalal

## Eksempel 1: Genbrugelige komponenter og fælles styling

### Hvor og hvorfor?

På forsiden genbruger vi blandt andet Button.astro i Hero, ServiceCard, CoreValues og Newsletter.

Et benspænd var, at knapperne skulle have forskellige udtryk afhængigt af, hvilken sektion de blev brugt i. Nogle knapper skulle have en baggrundsfarve, mens andre skulle vises som tekst med en pil.

Vi valgte derfor at udvide vores Button-komponent med forskellige varianter frem for at oprette nye knapper med samme kode.

### Relevant kode

```astro
<Button variant="light">
  Get in touch
</Button>

<Button variant="text-dark" showArrow={true}>
  Read more
</Button>
```

Ved hjælp af props kan vi ændre knappens udseende og vælge, om den skal have en pil. På den måde kan vi genbruge den samme komponent flere steder.

### Afprøvning og ændringer

- **Vi testede:** Knapperne på forskellige baggrundsfarver og i forskellige sektioner på forsiden.
- **Vi observerede:** At knapperne havde brug for forskellige farver og udtryk for at passe til Figma-designet.
- **Vi ændrede eller mangler:** Vi tilføjede flere varianter til Button-komponenten og fjernede overflødige props, som komponenten ikke anvendte. Under kodegennemgangen opdagede vi også, at teksten "Read more" var forsvundet fra vores Core Values-kort. Vi rettede dette ved at tilføje teksten mellem Button-komponentens åbne og lukkede tag.

## Eksempel 2: Dekorative grid-mønstre med moderne CSS

### Hvor og hvorfor?

I Hero og CoreValues indeholder Figma-designet et dekorativt grid-mønster i baggrunden.

Et benspænd var at genskabe mønsteret, så det lignede designet, uden at det påvirkede tekstens læsbarhed eller krævede et ekstra billede.

Vi valgte at lave mønsteret med CSS-gradients og et pseudo-element. Derudover brugte vi mask-image til at få mønsteret til gradvist at fade ud.

### Relevant kode

```css
background-image:
  linear-gradient(to right, var(--color-action) 1px, transparent 1px),
  linear-gradient(to bottom, var(--color-action) 1px, transparent 1px);

background-size: 60px 60px;
```

### Afprøvning og ændringer

- **Vi testede:** Mønsterets placering, størrelse og synlighed ved at sammenligne hjemmesiden med Figma-designet.
- **Vi observerede:** At mønsteret kunne blive for tydeligt og tage opmærksomheden fra teksten og kortene.
- **Vi ændrede eller mangler:** Vi justerede opacity, placering og mask-image, så mønsteret blev mere diskret og bedre passede til designet.

Fordelen ved denne løsning er, at vi kan justere farver, størrelse og synlighed direkte i CSS uden at skulle eksportere et nyt billede fra Figma.

## Eksempel 3: Dynamiske data og fejlhåndtering

### Hvor og hvorfor?

Vi henter dynamisk indhold fra API'et i blandt andet Services.astro og FinancialProjections.astro.

Et benspænd var at sikre, at hjemmesiden stadig kunne vises, hvis API'et ikke var tilgængeligt. Vores første løsning hentede data direkte med fetch() uden at håndtere eventuelle fejl.

Vi valgte derfor at tilføje try/catch, kontrollere API'ets svar og bruge fallback-data.

### Relevant kode

```js
let serviceData;

try {
  const response = await fetch("https://ftk-api.pages.dev/services");
  if (!response.ok) {
    throw new Error("Kunne ikke hente services");
  }

  serviceData = await response.json();
} catch (error) {
  console.error(error);
  serviceData = [];
}
```

Hvis API-kaldet lykkes, gemmes dataene i serviceData. Hvis der opstår en fejl, bruges et tomt array i stedet.

Det betyder, at vi stadig kan bruge .map() til at vise vores ServiceCard-komponenter, uden at siden går ned, hvis dataene mangler.

### Afprøvning og ændringer

- **Vi testede:** At indholdet stadig blev vist efter ændringen, og at projektet kunne bygges med npm run build.
- **Vi observerede:** At projektet blev bygget uden fejl, og at alle sider blev genereret.
- **Vi ændrede eller mangler:** Vi tilføjede fejlhåndtering og fallback-data i både Services og FinancialProjections. FinancialProjections har desuden et tjek i sliderens JavaScript, så funktionen ikke forsøger at flytte kort, hvis der ikke er nogen kort at vise.

Et næste skridt er at teste fallback-løsningen ved bevidst at gøre API'et utilgængeligt og kontrollere, hvordan siden ser ud.

## Fallback og robusthed

- **Fallback/progressive enhancement:** Vi har brugt mask-image til at skabe en blød overgang i vores dekorative grid-mønstre. Hvis browseren ikke understøtter mask-image, vil selve grid-mønsteret stadig kunne vises, men uden den bløde overgang. Mønsteret er dekorativt, så det påvirker ikke adgangen til sidens indhold.
  Reference: [MDN – mask-image](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/Properties/mask-image)

- **Defensive CSS:** I vores Services-komponent bruger vi CSS Grid til at placere kortene:

```css
grid-template-columns: repeat(3, minmax(0, 1fr));
```

Ved at bruge minmax(0, 1fr) kan kolonnerne tilpasse sig den tilgængelige plads, frem for at deres minimumsbredde alene bestemmes af indholdet.

I CoreValues har vi desuden valgt at lade kortene have forskellige højder afhængigt af tekstens længde. Det passer til Figma-designet og undgår, at teksten bliver presset ind i en fast højde.

- **Global CSS og komponent-CSS:**
  Vi bruger global.css til fælles typografi og generelle styles, som skal gælde på tværs af hjemmesiden.

Farver, afstande og andre designværdier er samlet i design tokens. Det gør det nemmere at holde hjemmesidens visuelle udtryk ensartet.

I de enkelte Astro-komponenter bruger vi lokal CSS til eksempelvis grid, flexbox, placering af billeder og styling af specifikke elementer.

Under kodegennemgangen fjernede vi blandt andet overflødig styling af en sektionsoverskrift i FinancialCard.astro, fordi den allerede blev håndteret gennem fælles og sektionens egen styling.

## Brug af AI

Vi har brugt AI som hjælp til at forstå tekniske løsninger, opdele arbejdet i komponenter, finde CSS-løsninger og gennemgå vores kode.

AI har blandt andet hjulpet med forslag til det dekorative grid-mønster, genbrug af Button-komponenten, fejlhåndtering af API-kald og løsning af merge-konflikter.

Vi har ikke ukritisk anvendt alle forslag. Vi har tilpasset løsningerne til vores eksisterende kode, design tokens og Figma-design. Vi har også fravalgt ekstra funktionalitet, når den ikke var nødvendig for opgaven.

Vi har kontrolleret løsningerne ved at sammenligne hjemmesiden med Figma, teste funktionaliteten i browseren og køre npm run build.

Vi har lært mere om genbrugelige komponenter, CSS-gradients, mask-image, API-fejlhåndtering og samarbejde med GitHub. AI har fungeret som sparringspartner, mens vi selv har implementeret, tilpasset og testet løsningerne.
