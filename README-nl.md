[English](./README.md) | [繁中版](./README-tw.md) | [简中版](./README-zh.md) | [العربية](./README-ar.md) | [বাংলা](./README-bn.md) | [Deutsch](./README-de.md) | [Ελληνικά](./README-el.md) | [Español](./README-es.md) | [فارسی](./README-fa.md) | [Français](./README-fr.md) | [हिंदी](./README-hi.md) | [Indonesia](./README-id.md) | [Italiano](./README-it.md) | [日本語](./README-ja.md) | [한국어](./README-ko.md) | [ລາວ](./README-lo.md) | [Македонски](./README-mk.md) | [മലയാളം](./README-ml.md) | [Монгол](./README-mn.md) | [Polski](./README-pl.md) | [Português (Brasil)](./README-pt_BR.md) | [Русский](./README-ru.md) | [ไทย](./README-th.md) | [Türkçe](./README-tr.md) | [Українська](./README-uk.md) | [Tiếng Việt](./README-vi.md)

# API Security Checklist
Checklist met de belangrijkste tegenmaatregelen bij het ontwerpen, testen en uitbrengen van een API.


---

## Authenticatie
- [ ] Gebruik geen `Basic Auth` Gebruik industrie standaarden (v.b. JWT, OAuth).
- [ ] Vind het wiel niet opnieuw uit voor `Authenticatie`, `Genereren van Tokens` en `Opslaan van Wachtwoorden`. Gebruik de standaarden.
- [ ] Gebruik `Max Retry` en Jail features in de login.
- [ ] Encrypt alle gevoelige data.

### JWT (JSON Web Token)
- [ ] Gebruik random ingewikkelde keys (`JWT Secret`) om brute forcing lastiger te maken.
- [ ] Haal het algoritme niet uit de payload. Dwing het algoritme af in de backend (`HS256` of `RS256`).
- [ ] Zet de token vervaltijd (`TTL`, `RTTL`) zo kort mogelijk.
- [ ] Sla geen gevoelige data op in de JWT payload, deze is [makkelijk](https://jwt.io/#debugger-io) te decoderen.
- [ ] Vermijd het opslaan van te veel gegevens. JWT wordt meestal gedeeld in headers en ze hebben een maximale grootte.

## Toegang
- [ ] Limiteer het aantal requests om DDoS en/of Bruteforce aanvallen te ontkrachten.
- [ ] Gebruik HTTPS aan de server zijde om MITM (Man In The Middle Attacks) tegen te gaan.
- [ ] Gebruik de `HSTS` header i.c.m SSL om een SSL Strip attack te ontkrachten.
- [ ] Schakel directoryvermeldingen uit.
- [ ] Sta voor privé-API's alleen toegang toe vanaf op de witte lijst geplaatste IP's/hosts.

## Authorization

### OAuth
- [ ] Valideer **ALTIJD** de `redirect_uri` op de server om alleen toegestane URL te accepteren.
- [ ] Probeer altijd een exchange voor code, niet voor tokens (sta `response_type=token` niet toe).
- [ ] Gebruik de `state` parameter met een random hash om CSRF op een OAuth authentication process te voorkomen.
- [ ] Definieer een standaard scope, en valideer deze scope parameter voor elke applicatie.

## Invoer
- [ ] Gebruik de correcte HTTP methode voor de operatie, `GET (lezen)`, `POST (schrijven)`, `PUT (vervangen/updaten)` and `DELETE (verwijderen)`.
- [ ] Valideer de `content-type` header bij een request Accept header (Content Negotiation) om alleen de ondersteunde formaten toe te staan (b.v. `application/xml`, `application/json` ... enz) en stuur een `406 Not Acceptable` response als de `content-type` niet ondersteund is.
- [ ] Valideer de `content-type` header van gestuurde data (b.v. `application/x-www-form-urlencoded`, `multipart/form-data`, `application/json` ... enz).
- [ ] Valideer de gebruiker invoer om veel voorkomende kwetsbaarheden te voorkomen (v.b. `XSS`, `SQL-Injection`, `Remote Code Execution` ... enz).
- [ ] Gebruik geen gevoelige data (`credentials`, `Wachtwoorden`, `security tokens`, of `API keys`) in de URL, maar gebruik de standaard Authorization header.
- [ ] Gebruik alleen versleuteling aan de serverzijde.
- [ ] Gebruik een API Gateway service voor caching, policies (b.v. `Quota`, `Spike Arrest`, `Concurrent Rate Limit`) en voor het dynamisch deployen van API middelen.

## Processing
- [ ] Controleer dat alle endpoints zijn beschermd achter de authenticatie om het omzeilen van authenticatie te voorkomen.
- [ ] Gebruik `/me/orders` i.p.v. `/user/654321/orders` om het 'lekken' van id's te voorkomen.
- [ ] Gebruik geen auto increment id's. Maak gebruik van `UUID`.
- [ ] Als je XML files parsed, controleer dat entity parsing niet aan staat om `XXE` (XML external entity attack) te voorkomen.
- [ ] Als je XML files parsed, controleer dat entity expansion niet aan staat om `Billion Laughs/XML bomb` te voorkomen via `exponential entity expansion attack`.
- [ ] Gebruik CDN voor het uploaden van bestanden.
- [ ] Als er met grote/mega hoeveelheden data gewerkt wordt, gebruik dan Workers en Queues om snel een response te geven en HTTP Blocking te voorkomen.
- [ ] Vergeet niet om de DEBUG mode uit te zetten.
- [ ] Gebruik niet-uitvoerbare stacks indien beschikbaar.

## Output
- [ ] Stel de `X-Content-Type-Options: nosniff` header in.
- [ ] Stel de `X-Frame-Options: deny` header in.
- [ ] Stel de `Content-Security-Policy: default-src 'none'` header in.
- [ ] Verwijder vingerafdruk headers - `X-Powered-By`, `Server`, `X-AspNet-Version` enz.
- [ ] Dwing `content-type` headers af voor de response. Als je `application/json` antwoordt, dan is de `content-type` : `application/json`.
- [ ] Stuur geen gevoelige data terug: `Gebruikersnamen`, `Wachtwoorden`, `security tokens`.
- [ ] Geef de correcte HTTP antwoord code terug op basis van de uitgevoerde operatie (v.b. `200 OK`, `400 Bad Request`, `401 Unauthorized`, `405 Method Not Allowed` ... enz).

## CI & CD
- [ ] Controleer het ontwerp en de implementatie met unit/integration test dekking.
- [ ] Gebruik een code review traject en controleer niet zelf je eigen code.
- [ ] Scan de API voor het naar productie zetten door AV software, niet alleen eigen code maar ook de libraries en andere gebruikte dependencies.
- [ ] Voer continu beveiligingstests (statische/dynamische analyse) uit op uw code.
- [ ] Controleer uw afhankelijkheden (zowel software en besturingssysteem) op bekende kwetsbaarheden.
- [ ] Ontwikkel een terugrol oplossing.

## Monitoring
- [ ] Use centralized logins for all services and components.
- [ ] Use agents to monitor all traffic, errors, requests, and responses.
- [ ] Use alerts for SMS, Slack, Email, Telegram, Kibana, Cloudwatch, etc.
- [ ] Ensure that you aren't logging any sensitive data like credit cards, passwords, PINs, etc.
- [ ] Use an IDS and/or IPS system to monitor your API requests and instances.


---

## Zie ook:
- [yosriady/api-development-tools](https://github.com/yosriady/api-development-tools) - Een verzameling nuttige bronnen voor het bouwen van RESTful HTTP+JSON API's.


---

Translation by | Vertaling door :[S.Holzhauer](https://github.com/SHolzhauer)

# Contribution
Voel u vrij om bij te helpen door deze repository te fork, wijzigingen aan te brengen, en pull requests in te dienen. Voor vragen kunt u ons mailen op `team@shieldfy.io`.
