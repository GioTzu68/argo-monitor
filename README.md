# ARGO · monitor remoto

Dashboard web **statica** per monitorare da remoto un BMS JK-PB tramite [ARGO](https://github.com/GioTzu68/argo).
Si collega a un broker **MQTT cloud** (HiveMQ Cloud) via WebSocket sicuro (WSS) e mostra i dati
real-time pubblicati dal dispositivo ESP32.

## Come funziona
- L'ESP32 (a casa, dietro NAT) pubblica lo stato del BMS sul broker cloud via TLS (utente *solo-publish*).
- Questa pagina si collega allo **stesso broker** via WSS (utente *solo-subscribe*, read-only).
- La **password non è nel codice**: viene chiesta nel browser e resta solo lì (`sessionStorage`).

Pagina pubblica, **dati protetti da password**: chi apre il link senza la password non vede nulla.

## Configurazione
Modifica il blocco `CFG` in [`index.html`](index.html) con l'hostname del tuo cluster HiveMQ:

```js
const CFG={
  host: "xxxxxxxx.s1.eu.hivemq.cloud", // hostname del cluster
  port: 8884,                          // WSS del broker
  user: "argo-sub",                    // utente solo-subscribe
  base: "argo/jkbms"                   // = CLOUD_MQTT_TOPIC del firmware
};
```

## Deploy su GitHub Pages
1. Push di questa repo su GitHub.
2. Settings → Pages → Source: `main` / root.
3. La dashboard sarà su `https://<utente>.github.io/<repo>/`.

## Sicurezza
- L'utente `argo-sub` è **read-only** (ACL sul broker): chi ha la password può solo *guardare*, non pubblicare.
- Per protezione massima si può cifrare il payload lato firmware e decifrarlo qui con una passphrase
  (non implementato: la protezione attuale è password + ACL sul broker).

Setup completo (broker + firmware): vedi `docs/CLOUD_MONITOR.md` nel repo ARGO.
