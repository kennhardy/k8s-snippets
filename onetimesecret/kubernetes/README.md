# k8s-onetimesecret

## Instruktioner
Denna deploy kommer publicera en onetimesecret server instans som kan nås på publikt ip och nodeport ```30211```. Följ instruktionerna enligt sidan, lägg gärna en issue om det inte fungerar.

## Bygg en docker image från officiella onetimesecret
```bash
git clone https://github.com/onetimesecret/onetimesecret.git
```
```bash
cd onetimesecret
```

Gör eventuella ändringar i ```./etc/config``` och ```./etc/locale/```. Rekommenderar att endast använda "sv_SE" och "en" som locales då det även finns inställningar i locales filerna. Bland annat email.incomingsupport.subject finns (ämnesraden för incoming support) finns med i locales-filerna.

```bash
docker build . -t onetimesecret
```

## Pusha din nybyggda image till eget docker registry
Tagga först din image med ditt eget registry (but ut addressen):
```bash
docker image tag onetimesecret registry.hardy.se/onetimesecret
```
Pusha imagen till eget docker registry (byt ut addressen):
```bash
docker image push registry.hardy.se/onetimesecret
```
Kom ihåg att även ändra till rätt registry, imagenamn och eventuell tag (latest) i [onetimesecret-deployment.yml](onetimesecret-deployment.yml#L17).


## Apply samtliga kubernetes manifests från detta repo
```bash
git clone https://github.com/kennhardy/k8s-snippets.git
```

```bash
cd k8s-snippets/onetimesecret/kubernetes/
```

Gör sedan alla nödvändiga ändringar i dina manifests. Bland annat behöver du förmodligen lägga till dina egna registry credentials i regcred secret för att kunna pulla image från registry. Denna secret refereras även till i deployment-filen. Kan även vara smart att köra den i en egen namespace.

För att skapa en base64 regcred kan man köra:
```bash
echo -n '{"auths":{"registry.hardy.se":{"username":"your-username","password":"your-password","auth":"base64-encoded-authentication-token"}}}' | base64
```
Byt sedan ut strängen ```BASE64encodedAuth==``` i [onetimesecret-secrets-regcred.yml](onetimesecret-secrets-regcred.yml#L4) med din base64 sträng.

När alla ändringar är fixade kan samtliga manifests köras med (säkerställ att man är i rätt directory):
```bash
kubectl apply -f *
```

## Felsökning
Min kube-dns vägrade slå upp "redis" och gav luddig fel i loggen. För att testa ifall den slår upp namnet korrekt använde jag:
```bash
kubectl run dnsutils --image=tutum/dnsutils --restart=Never --rm -it -- nslookup onetimesecret-service.default.svc.cluster.local
```
Kom ihåg att ändra namespace ifall liknande händer: ```<service-name>.<namespace>.svc.cluster.local```

Såhär såg error-meddelande ut från app när den inte kunde prata med eller få kontakt med redis:
```bash
kubectl logs <pod> -c app
```
```/usr/local/bundle/gems/redis-2.2.2/lib/redis/connection/ruby.rb:26:in `initialize': getaddrinfo: Name or service not known (SocketError)```

*PS!* hela manifestet är skrivet till att köras i default namespace. Ändra allteftersom.
