# k8s-snippets

## Instruktioner
Denna deploy kommer publicera en onetimesecret server som kan nås på publikt ip och nodeport 30211.

```bash
git clone https://github.com/kennhardy/k8s-snippets/tree/main/onetimesecret
```

Kom ihåg att bygga en image från: https://github.com/onetimesecret/onetimesecret.git

## Bygga image
git clone https://github.com/onetimesecret/onetimesecret.git
cd onetimesecret

Gör eventuella ändringar i ./etc/config och locales-filen. Rekommenderar att endast använda SE + ENG som locales då det även finns inställningar i locales filerna.

docker build . -t onetimesecret

## Pusha image till eget docker register
Lokalisera din image: docker image ls
Tagga din image:
```bash
docker tag onetimesecret registry.1webhost.xyz/onetimesecret
```
Pusha image:
```bash
docker push registry.1webhost.xyz/onetimesecret
```
Kom ihåg att även ändra till rätt registrar, imagenamn och tag i [onetimesecret-deployment.yml](onetimesecret-deployment.yml#L17).


## Apply samtliga kubernetes manifests
```bash
cd k8s-snippets/onetimesecret
```
Och kör sedan:
```bash
kubectl apply -f *
```

## Felsökning
Min kube-dns vägrade slå upp "redis" och gav luddig fel i loggen. För att testa ifall den slår upp namnet korrekt använde jag:
```bash
kubectl run dnsutils --image=tutum/dnsutils --restart=Never --rm -it -- nslookup onetimesecret-service.default.svc.cluster.local
```
Kom ihåg att ändra namespace ifall liknande händer: ```<service-name>.<namespace>.svc.cluster.local```

Såhär såg error-meddelande ut från app:
```bash
kubectl logs <pod> -c app
```
```/usr/local/bundle/gems/redis-2.2.2/lib/redis/connection/ruby.rb:26:in `initialize': getaddrinfo: Name or service not known (SocketError)```

*PS!* hela manifestet är skrivet till att köras i default namespace. Ändra allteftersom.
