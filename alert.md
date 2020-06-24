# Monitoraggio nodi Grafana + Prometheus

## Impostazione del server vicino

In questa guida, vorrei mostrare come è possibile monitorare il nodo usando l'esempio del nodo Vicino

Esegui Node Exporter sul server Near:

```
sudo docker run -dit \ 
    --restart always \ 
    --volume / proc: / host / proc: ro \ 
    --volume / sys: / host / sys: ro \ 
    --volume /: / rootfs: ro \ 
    --name node-exporter \ 
    -p 9100: 9100 prom / node-exporter: latest \ 
    --path.procfs = / host / proc \ 
    --path.sysfs = / host / sys
```

È necessario aprire la porta 9100 in modo che Prometheus possa raccogliere i dati del nostro nodo.
Esegui Near Exporter sul server Near:

```
git clone https://github.com/masknetgoal634/near-prometheus-exporter 
cd near-prometheus-exporter
sudo docker build -t near-prometeo-esportatore.
```

Esegui finestra mobile:

```
sudo docker run -dit \ 
    --restart always \ 
    --name near-exporter \ 
    --network = host \ 
    -p 9333: 9333 \ 
    near-prometheus-exporter: latest / dist / main -accountId <POOL_ID>
 ```   
    
<POOL_ID> : il pool che verrà monitorato

È necessario aprire la porta 3030 e la porta 9333 in modo che Prometheus possa raccogliere i dati del nostro nodo e inviarli al server.

## Monitoraggio della configurazione del server

Dopo aver configurato il nostro server Near per raccogliere e inviare statistiche, dobbiamo configurare il server di monitoraggio stesso.

Eseguire un aggiornamento del sistema e installare i pacchetti necessari:

```
sudo apt-get update 
sudo apt installa git docker.io -y
```

Cloniamo la cartella e seguiamo il percorso ecc:

```
git clone https://github.com/masknetgoal634/near-prometheus-exporter 
cd near-prometheus-exporter / etc
```

Ora dobbiamo aprire il file prometheus / prometheus.yml e apportare modifiche sostituendo l'indirizzo IP del nostro server Near:
```
sudo nano prometheus/prometheus.yml
```
<NODE_IP_ADDRESS> sostituisce con l'indirizzo IP del nostro nodo Near


Dopo di che cambiamo l'URL con l'indirizzo IP del nostro server di monitoraggio, lasciamo la porta come prima

```
sudo nano grafana/provisioning/datasources/all.yml
```

In caso contrario, Prometeo potrebbe non visualizzare il nostro database e genererà un errore.
Avvia Prometeo:

```
sudo docker run -dti \ 
    --restart always \ 
    --volume $ (pwd) / prometheus: / etc / prometheus / \ 
    --name prometheus \ 
    --network = host \ 
    -p 9090: 9090 prom / prometheus: latest \ 
    - -config.file = / etc / prometheus / prometheus.yml
```

Successivamente, dobbiamo configurare il nostro server SMTP per inviare notifiche:

```
sudo nano grafana/custom.ini
```

Passiamo ai dati del nostro indirizzo email su Google:
```
[smtp]
enabled = true
host = smtp.gmail.com:587 
user = <your_gmail_address>
password = <your_gmail_password>
;cert_file =
;key_file =
skip_verify = true
from_address = <your_gmail_address>
from_name = Grafana
```
Scopriamo l'ID utente con il quale siamo:

```
id -u
```

Il nostro numero 1001

Avvia Grafana ( sostituisci 1001 con il tuo ID! ):

```
sudo chown -R 1001: 1001 grafana / * 
sudo docker run -dit \ 
    --restart always \ 
    --volume $ (pwd) / grafana: / var / lib / grafana \ 
    --volume $ (pwd) / grafana / provisioning: / etc / grafana / provisioning \ 
    --volume $ (pwd) /grafana/custom.ini:/etc/grafana/grafana.ini \ 
    --user 1001 \ 
    --network = host \ 
    --name grafana \ 
    -p 3000: 3000 grafana / grafana
```    
Andiamo a Grafana su: http://localhost:3000, localhost cambiamo il monitoraggio del server all'indirizzo IP.

Inserisci il nome utente admin password admin e imposta una nuova password
