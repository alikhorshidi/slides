# While-Schleife

## While-Schleife

Eine if-Abfrage führt einen Codeblock _einmal_ aus, wenn ein Kriterium zutrifft

Eine while-Schleife wiederholt einen Codeblock, solange ein Kriterium zutrifft

Beispiel:

```py
a = 1

while a < 2000:
    print(a)
    a = a * 2
```

## While-Schleife

Übungen:

- Zahlenraten mit mehreren Versuchen
- Schleife, die die Zahlen 1 bis 10 ausgibt
- Schleife, die die Zahlen der 7er-Reihe des Einmaleins ausgibt
- Schleife, die die Liste `[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]` erstellt
- Rechentrainer mit Zufallsaufgaben
- Einkaufsliste

## While-Schleife

Beispiel: Einkaufsliste

Beispielhafter Programmlauf:

```txt
enter an item or "x" to quit:
milk
enter an item or "x" to quit:
bread
enter an item or "x" to quit:
apples
enter an item or "x" to quit:
x
your shopping list is:
["milk", "bread", "apples"]
```

## Continue & break

Die Schlüsselwörter `continue` und `break` können verwendet werden, um einen Schleifendurchlauf bzw die ganze Schleife zu beenden

Bei verschachtelten Schleifen beziehen sie sich auf die innerste Schleife

## Continue & break

Beispiel:

```py
a = 1

while True:
    a = a * 2
    print(a)
    if (a > 1000):
        break
```
