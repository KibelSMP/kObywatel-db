# Katalog map-points

Ten katalog umożliwia dzielenie danych `map-points.json` na wiele mniejszych plików.
Serwer automatycznie:

1. Sprawdza czy istnieje katalog `public/map-points/`.
2. Jeśli tak – wczytuje wszystkie pliki zakończone `.json` (kolejność alfabetyczna).
3. Scala:
   - `meta` – z pierwszego pliku, który ją dostarcza (np. `meta.json`).
   - `categories` – unia; pierwsza definicja kategorii wygrywa.
   - `points` – wszystkie punkty; duplikaty po `id` są pomijane (pierwszy wygrywa).
4. Następnie dokłada dynamicznych graczy i zwraca pod `/map-points.json`.

## Format plików
Możesz użyć jednego z wariantów:

### Pełny fragment
```jsonc
{
  "meta": { "projection": "simple-1px-4m" },
  "categories": { "miasto": { "label": "Miasto", "color": "#ff4d4d" } },
  "points": [ { "id": "city-foo", "name": "Foo", "category": "miasto", "x": 0, "z": 0 } ]
}
```
### Tylko punkty
```jsonc
{ "points": [ { "id": "city-bar", "name": "Bar", "category": "miasto", "x": 10, "z": -40 } ] }
```
### Pojedynczy punkt jako obiekt
```jsonc
{ "id": "city-baz", "name": "Baz", "category": "miasto", "x": 5, "z": 5 }
```

## Kolejność i duplikaty
- Kolejność scalania = alfabetyczna po nazwie pliku.
- Duplikaty `id` punktów ignorowane (logika: pierwszy wygrywa).
- Jeśli potrzebujesz nadpisać punkt – usuń jego wcześniejszy plik.

## Migracja ze starego pliku
1. Skopiuj sekcję `meta` i `categories` do `meta.json`.
2. Podziel tablicę `points` na mniejsze pliki (np. po województwach / kategoriach).
3. Usuń lub zarchiwizuj dawny `public/map-points.json` (może zostać – nie będzie użyty, jeśli katalog istnieje i ma pliki).

## Notatki
### Usunięte kategorii
```jsonc

        "infrastruktura": {
            "label": "Infrastruktura",
            "color": "#3b82f6"
        },
```

## Debug
Serwer loguje: `Załadowano wieloplikowe punkty ...` przy przeładowaniu.
Zmiana mtime któregokolwiek pliku powoduje regenerację cache przy następnym żądaniu.
