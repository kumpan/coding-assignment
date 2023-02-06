
# Kodprov - American API!

Kumpan har fått i uppdrag av en amerikansk myndighet att ta fram en app som visualiserar hur det egentligen står till med befolkningen i USA. För att komma igång med appen behöver vi först en backend att prata med. Det är här du kommer in! Din uppgift blir att ta fram en backend-applikation som serverar ett API med data om amerikansk befolkning.

## Datakällor
Som datakälla kan man använda tjänsten dataUSA: https://datausa.io/about/api/

Med olika API-anrop till DataUSAs api kan man få fram t ex:
- Befolkning per stat https://datausa.io/api/data?drilldowns=State&measures=Population&year=latest 
    - Year kan sättas till “latest” för senast tillgängliga data eller ett numerärt värde, eller så kan man lämna ute parametern helt
- Befolkning för en enskild stat (givet ett “Geography” id, i detta fall 04000US10 som är Delaware: https://datausa.io/api/data?measures=Total+Population&Geography=04000US10
- Inkomst för hushåll för en enskild stat: https://datausa.io/api/data?measure=Household%20Income,Household%20Income%20Moe&geo=04000US10

API:et är dåligt dokumenterat, men man kan få ytterliggare ledtrådar genom att kika på network requests som görs när man surfar runt på webbplatsen, exv genom att scrolla nedåt på https://datausa.io/profile/geo/delaware

## Kravspecifikation
Du ska utveckla en körbar applikation som serverar ett API med information om amerikanska stater. Språket du använder är valfritt, så länge vi kan köra applikationen. 

Följande endpoints bör finnas:

### /top
En lista på amerikanska stater, sorterad på befolkningsmängd. Endpointen ska även stödja en valbar parameter “limit” som anger hur många stater topplistan ska innehålla. Alltså ska ett request mot `/top?limit=5` svara med en sorterad lista på de 5 mest befolkade staterna. Om limit är utelämnad bör du tolka detta som en limit på 10.

Responset bör vara en sorterad JSON-lista, där varje enskilt objekt bör innehålla: 
- Namn på staten
- Folkmängd
- Geography ID

Exempel-respons:
```json
[
    {
        "name": "Delaware",
        "population": 500123,
        "id": "04000US10"
    },
    {
        "name": "Michigan",
        "population": 499123,
        "id": "..."
    }
]
```


### /single

Här vill vi få reda på information om en enskild stat. Endpointen ska kräva parametern “id”, som ska innehålla en “Geography ID”. Ett request mot `/single?id=04000US10` ska alltså svara med information om Delaware. 

Förutom “id” ska endpointen även stödja de valbara parametrarna:
- includeIncome — boolean som kan sättas till true eller false. Om den är inkluderad ska response inkludera statens median hushållsinkomst (“household income”). Om parametern saknas, tolka det som “false”.
- includePopulation - boolean som kan sättas till true eller false. Om den är satt till true response inkludera en lista på år, och hur många invånare staten hade det året. Listan bör vara sorterad på antal befolkning. Om parametern saknas, tolka det som “false”.

Responset bör vara ett JSON-objekt och bör innehålla:
- Namn på staten
- householdIncome, om motsvarande parameter är satt till true
- populationData, om motsvarande parameter är satt till true

Exempel-respons, på ett request till url `/single?id=04000US10&includeIncome=true&includePopulation=true`:

```json
{
    "name": "Delaware",
    "householdIncome": 370953,
    "populationData": 
    [
        {
            "year": "2019",
            "population": 1006
        },
        {
            "year": "2018",
            "population": 697
        },
        {
            "year": "2017",
            "population": 450
        }
    ]
}
```

## Ytterliggare krav
- Båda endpoints bör vara HTTP GET
- I exemplet ovan har parametrar satts som query parameters, men det är tillåtet att använda andra typer av parametrar, så länge det blir använtbart!
- Response bör vara JSON
- Requests som inte matchar spec ovan bör svara med lämplig felkod (t ex 404, om användaren försöker requesta en URL som inte finns)
- Oväntade fel (t ex din datakälla är nere) bör svara med lämplig felkod
- Enhetstester är inte ett krav, men ett uppskattat tillägg

## Tidsåtgång

Kodprovet bör inte ta längre än ett par timmar. Vi förväntar oss alltså inte att du ska lägga flera dagar på detta, och kan därför acceptera vissa saknade features. Det är dock uppskattat om du funderar på hur du hade vidareutvecklat applikationen om du hade oändligt med tid! 