# KlarText

Das Trinkspiel für eure WhatsApp-Chats: Ein Wort, ein Chat – wer schätzt am besten, wie oft es vorkommt?

Das Spiel steckt in einer einzigen Datei: `index.html`. Dazu kommen `manifest.webmanifest` und der Ordner `icons/`, damit man KlarText in Chrome als App installieren kann (Menü → „App installieren“ bzw. auf dem Handy „Zum Startbildschirm hinzufügen“).

## Spielmodi

| Modus | Wie | Internet |
|---|---|---|
| 📱 **Ein Gerät für alle** | Ihr reicht ein Handy/Laptop herum. | Nicht nötig – einfach `index.html` öffnen. |
| 🌐 **Online** | Jeder spielt am eigenen Handy in einem Raum mit Code. | Nötig – läuft über Firebase. |

## Datenschutz

- **Chats verlassen nie das Gerät, auf dem sie hochgeladen wurden.** Auch online wird nur auf diesem
  Gerät gezählt. An den Server gehen nur: Spielernamen, Dateiname und Anzahl der Nachrichten, das Wort,
  die Tipps, das Ergebnis und **bis zu 3 Beweis-Nachrichten pro Runde**.
- Daten liegen nur im Tab (`sessionStorage`) und sind beim Schließen weg. „Alle Daten löschen“ /
  „Raum verlassen“ entfernt sie sofort.
- Online-Räume löschen sich nach 24 Stunden Inaktivität automatisch (TTL), der Host kann sie jederzeit auflösen.
- Anmeldung ist anonym – kein Konto, keine E-Mail.
- Nur Mitglieder können einen Raum lesen, Räume lassen sich nicht auflisten, Tipps der anderen sind bis
  zum Aufdecken gesperrt (siehe `firestore.rules`).
- Eine Content-Security-Policy erlaubt Verbindungen ausschließlich zu Firebase. Der Ein-Gerät-Modus baut
  gar keine Verbindung auf.

## Online-Modus einrichten (einmalig, ~10 Minuten)

Voraussetzung: [Node.js](https://nodejs.org) und ein Google-Konto. Firebase ist für diese Nutzung kostenlos (Spark-Tarif).

1. **Projekt anlegen:** <https://console.firebase.google.com> → „Projekt hinzufügen“.
   Google Analytics **deaktivieren**.
2. **Anonyme Anmeldung aktivieren:** *Build → Authentication → Jetzt starten → Sign-in method →
   Anonym → Aktivieren*.
3. **Datenbank anlegen:** *Build → Firestore Database → Datenbank erstellen*.
   Standort **`eur3 (Europa)`** oder **`europe-west3 (Frankfurt)`** wählen (lässt sich später nicht ändern),
   Modus „Produktion“.
4. **Automatisches Löschen (TTL) einrichten:** *Firestore → Time-to-live (TTL) → Richtlinie erstellen*,
   jeweils mit dem Feld `expiresAt`, für die Sammlungsgruppen:
   - `rooms`
   - `guesses`
   - `taken`

   Falls TTL in eurem Tarif nicht angeboten wird: kein Problem – Räume werden trotzdem gelöscht, sobald der
   Host sie auflöst, und sie enthalten ohnehin keine Chats.
5. **Hochladen** (im Ordner dieses Repos):
   ```bash
   npm install -g firebase-tools
   firebase login
   firebase use --add          # euer Projekt auswählen
   firebase deploy --only hosting,firestore:rules
   ```
6. Fertig – die Adresse steht in der Ausgabe, z. B. `https://euer-projekt.web.app`.
   Die Firebase-Konfiguration holt sich die Seite automatisch, es muss nichts eingetragen werden.

> Läuft die Seite **nicht** über Firebase Hosting (z. B. GitHub Pages), die Werte aus
> *Projekteinstellungen → Meine Apps → Web-App* in `FIREBASE_CONFIG` in `index.html` eintragen.

### Lokal testen

```bash
firebase emulators:start --project demo-klartext
```

Dann <http://127.0.0.1:5000/?emulator> in mehreren Browserfenstern (z. B. normal + privat) öffnen.

## Rechtliches (privat, nicht kommerziell)

Für ein rein privates Spiel unter Freunden ohne kommerzielle Absicht greift in der Regel die
Haushaltsausnahme der DSGVO, und eine Impressumspflicht besteht meist nicht. Teilt den Link am besten nur
in eurem Freundeskreis. Das ist keine Rechtsberatung.
