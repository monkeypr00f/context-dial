# Context Dial

Terminale fisico opzionale ESP32-S3 con UI LVGL per
[Contextual Controls](https://github.com/monkeypr00f/contextualcontrols).
Questo repository contiene solo firmware, interfaccia e documentazione hardware:
non contiene il componente Home Assistant né un secondo motore predittivo.
Contextual Controls si può installare e usare senza questo dispositivo.

## Compatibilità

Il firmware richiede le aggiunte Context Dial integrate localmente sulla release
Contextual Controls 0.10.5: tipo LIGHT, input adjust e sensore feedback.
La sola release pubblicata 0.10.5 non include tutte queste aggiunte.
Per ora usare il componente HA del merge associato, non una versione precedente.
L'integrazione richiede Home Assistant Core 2026.9.3 e Python 3.14.2 o successivi.

ESPHome verificato: 2026.9.1. La configurazione e la generazione C++ passano;
build binaria completa e prova hardware restano da verificare.
Le versioni future del firmware avranno release indipendenti dall'integrazione.

## Hardware

ESP32-S3-WROOM-1 N16R8, flash 16 MB, PSRAM octal 8 MB, TFT EstarDyn ST7789
240×320, encoder EC11 e pulsante KO separato.
La configurazione attuale usa il layout portrait 240×320 e la rotazione corretta
per il montaggio mostrato durante il collaudo. Non cambiare solo la rotazione
per ottenere un layout landscape: anche le coordinate dei widget vanno adattate.

| Segnale | GPIO |
| --- | --- |
| Encoder TRA / TRB | 4 / 5 |
| Encoder PSH / pulsante KO | 6 / 7 |
| TFT RES / DC / CS | 8 / 9 / 10 |
| TFT MOSI / CLK | 11 / 12 |
| BLK PWM | 3 |
| VDD / GND | 3V3 / GND |

GPIO3 è un pin di strapping: evitare pull-up/down esterni incompatibili con il
boot. Il cablaggio esistente è stato validato, ma resta un vincolo hardware.

## Installazione

1. Copiare [esphome/context_dial.yaml](esphome/context_dial.yaml) nella cartella
   delle configurazioni ESPHome.
2. Impostare device_name, friendly_name, terminal_id e cc_entity_prefix.
   Per esempio terminal_id cc_cucina usa sensor.contextual_controls_cc_cucina.
3. Creare secrets.yaml usando [l'esempio](esphome/secrets.example.yaml),
   sostituendo tutti i valori fittizi. Generare una chiave API personale:
   non usare la chiave di esempio su un dispositivo reale.
4. Installare il componente Contextual Controls compatibile in HA e riavviare.
5. In Configura → Terminali fisici inserire lo stesso terminal_id e selezionare
   Salotto, Sala da pranzo e Cucina, o le aree desiderate.
6. Compilare e installare dal pannello ESPHome, la prima volta tramite USB.
   Gli aggiornamenti successivi possono usare OTA.
7. Nella configurazione del dispositivo ESPHome in HA abilitare
   “Allow the device to perform Home Assistant actions”.

Il dispositivo non include credenziali reali. Non committare secrets.yaml,
artefatti di compilazione o copie personali della configurazione.

## Interazione

- Rotazione: navigazione; in modalità valore, modifica immediata in HA.
- Click encoder: entra/esegue/conferma. Su una luce dimmerabile entra nella
  regolazione della luminosità della luce HA, non del display.
- KO: torna al menu; non annulla valori già applicati.
- Doppio click KO: apre Display ESPHome, per la retroilluminazione locale.
- Dopo 30 secondi il display si attenua; dopo 120 si spegne.
  Il primo input a display spento risveglia soltanto, senza inviare comandi.

UI scura con card arrotondate, focus evidente, colori per categoria e schermate
valore, feedback, attesa/offline e impostazioni display. Sono visibili fino a
cinque controlli complessivi dalle aree assegnate, non cinque per ogni area.

## Confine tra i progetti

Home Assistant fornisce contesto, slot ordinati, stato, tipo, limiti e revisione.
Il Dial invia terminale, slot, delta e revisione attraverso terminal_input.
Non invia entity_id eseguibili e non decide ranking, sicurezza o regole servizi.
Il [protocollo](docs/PROTOCOL.md) descrive il contratto attuale.

## Verifiche

La CI di questo repository valida la configurazione e genera il codice C++ con
ESPHome usando credenziali esclusivamente fittizie. Non è una prova hardware
né una compilazione binaria completa. Prima di una release verificare sul Dial:
orientamento/colori, encoder, luce HA, KO, tre aree e risveglio senza azione.

Licenza MIT; firmware estratto conservando la cronologia Git disponibile.
