# docker-onetimesecret

## Instruktioner
Ifall man önskar en docker-compose version av onetimesecret kan man göra enligt denna instruktion.

## Klona och göra ändringar
```bash
git clone https://github.com/onetimesecret/onetimesecret.git
```
```bash
cd onetimesecret
```

Gör eventuella ändringar i ```./etc/config``` och ```./etc/locale/```. Rekommenderar att endast använda "sv_SE" och "en" som locales då det även finns inställningar i locales filerna. Bland annat email.incomingsupport.subject finns (ämnesraden för incoming support) finns med i locales-filerna.

Gör sedan nödvändiga ändringar i  [docker-compose.yml](docker-compose.yml) filen innan du kör den.

## Bygga och starta
Docker-compose filen har en "build ." i sig så den kommer bygga image direkt:
```bash
docker-compose up
```

Går även göra en build direkt ifall man gjort några ändringar. Kom isf ihåg att starta om efter bygget.
```bash
docker-compose build
```
```bash
docker-compose restart
```
