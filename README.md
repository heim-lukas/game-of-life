# Game of Life

Conway's Game of Life im Terminal (mit `curses`).

## Voraussetzungen

- Python 3.10 oder neuer
- Unter Windows wird `windows-curses` automatisch mitinstalliert

## Installation

```powershell
python -m venv venv
venv\Scripts\activate
pip install -e .
```

Unter Linux/macOS: `source venv/bin/activate`

## Ausführen

```
python -m game_of_life
```

oder nach der Installation:

```
game-of-life
```

### Optionen

| Option | Beschreibung | Standard |
|---|---|---|
| `-p`, `--pattern` | Muster (Blinker, Toad, Beacon, Pulsar, Penta Decathlon, Glider, Glider Gun, Bunnies) | `Blinker` |
| `-a`, `--all` | alle Muster nacheinander zeigen | aus |
| `-v`, `--view` | Ansicht | `CursesView` |
| `-g`, `--gen` | Anzahl Generationen | `10` |
| `-f`, `--fps` | Bilder pro Sekunde | `7` |
| `--version` | Version anzeigen | |

Beispiel:

```
python -m game_of_life -p Pulsar -g 30 -f 10
```

Das Terminal muss groß genug sein (mindestens ca. 40 Zeichen breit und 21 Zeilen hoch), sonst erscheint eine Fehlermeldung.

## Credits

Dieses Projekt basiert auf dem Tutorial
[Conway's Game of Life in Python](https://realpython.com/conway-game-of-life-python/)
von Real Python.
