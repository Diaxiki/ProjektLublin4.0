# Projekt Lublin – dokumentacja strony

Statyczna strona (jeden plik `index.html` + `mods.json` + katalog `image/`) dla mapy Lublina do OMSI 2. Nie wymaga budowania ani serwera aplikacyjnego – wystarczy dowolny hosting plików statycznych.

## 1. Struktura plików

```
/
├── index.html          – cała strona (HTML + CSS + JS)
├── mods.json           – lista modów         
└── image/
    ├── Metaliczny_logo_Projekt_Lublin.png
    ├── Nowa_miniatruka1.png, image.png, Bogdan.png   – miniatury wpisów
    ├── Przebiegtrasy.png                             – podkład mapy trasy linii 151 (1376×752)
    ├── obrazyPokazujace*.png                         – galeria
    └── Lublin/                                       – 4 zdjęcia w zakładce Info
```


## 2. Zakładki (routing)

Strona działa na jednym pliku. Zakładki przełącza się po haszu w adresie:

| Adres        | Sekcja                |
|--------------|-----------------------|
| `#glowna`    | Aktualności (domyślna)|
| `#download`  | Pobieranie + archiwum |
| `#mod`       | Mody                  |
| `#rozwoj`    | Postęp linii 151      |
| `#galeria`   | Galeria               |
| `#info`      | Info o Lublinie       |

Każda zakładka to `<section class="page" id="page-NAZWA">`. Aktywna dostaje klasę `show`.

## 3. Aktualności (strona główna)

Wpis to blok `<article class="card post">` w kontenerze `<div class="posts" data-paginate="4">`.

- Nowy wpis: skopiuj istniejący `<article>` i zmień obraz, datę, tytuł i opis.
- Paginacja jest automatyczna. Liczbę wpisów na stronę zmienia atrybut `data-paginate` (domyślnie 4).
- Przycisk „Wesprzyj twórców” (`.support-box`) – zmień `href`, aby wskazywał inny adres wpłat.
- Blok `.support` z listą ostatnich wpłat (Suppi) jest w CSS, ale obecnie nie ma go w HTML. Jeśli chcesz go przywrócić, dodaj go wewnątrz `<div class="layout">` obok kolumny z wpisami.

## 4. Download i archiwum

- Karty pobierania: `<div class="card dl">` w `.dl-grid`. Zmieniasz nazwę, wersję (`.meta`), opis i `href`.
- Archiwum wersji: każdy `<div class="card ver">` w `.arch` to jedna wersja (tag, tytuł, opis, link). Aby dodać wersję, skopiuj blok.

## 5. Mody (`mods.json`)

Dodawanie i edycja modów odbywa się wyłącznie w `mods.json`, bez ruszania HTML.

### Format pliku

```json
{
  "categories": [
    { "id": "pojazd", "label": "Pojazdy" },
    { "id": "malowanie", "label": "Malowania" }
  ],
  "mods": [
    {
      "id": "unikalny-identyfikator",
      "title": "Nazwa moda",
      "author": "Autor",
      "date": "2026-11-01",
      "description": "Krótki opis.",
      "categories": ["pojazd"],
      "media": { "type": "image", "src": "image/mody/podglad.png", "alt": "Opis obrazka" },
      "downloads": [
        { "label": "Pobierz z Dysku Google", "url": "https://..." }
      ]
    }
  ]
}
```

### Zasady

- Sugerowane kategorie: `malowanie`, `pojazd`, `blender`, `siec`, `ai`, `inne`. Lista kategorii pochodzi z klucza `categories` i z niej generowane są filtry.
- `date`: format `RRRR-MM-DD` lub `DD.MM.RRRR`.
- `media.type`: `image` (domyślnie) lub `video`. Dozwolone rozszerzenia: png, jpg, jpeg, webp, gif, mp4, webm. Ścieżka musi być lokalna, względna, bez `..` i bez schematu (`http:` itp.).
- `downloads`: jeden wpis daje zwykły przycisk, kilka wpisów daje rozwijane menu. Dozwolone są tylko adresy `https://`.
- Kolejność w pliku decyduje o układzie, gdy daty lub nazwy są równe (wyżej w pliku = wyżej na liście).
- Filtry kategorii działają jako LUB (mod pasuje, jeśli ma którąkolwiek z zaznaczonych kategorii). Aby zmienić na ORAZ, w funkcji `applyModsFilter` zamień `checked.some(...)` na `checked.every(...)`.
- Liczba modów na stronę: stała `MODS_PER_PAGE` w skrypcie (domyślnie 4).
- Limity: do 500 modów, do 30 kategorii, do 6 kategorii na mod, do 10 linków na mod.
- Dane z JSON są sanityzowane i escapowane, więc nieprawidłowe wpisy są po prostu pomijane.

## 6. Rozwój – linia 151

### Listy zadań
- Zrobione: elementy `<li>` w `<ul class="task-list done-list">`.
- Do zrobienia: elementy `<li>` w `<ul class="task-list todo-list">`.
- Liczniki „Zrobione / Do zrobienia” liczą się automatycznie z liczby `<li>`. Uwaga: pusty `<li></li>` też jest liczony jako pozycja.

### Mapa trasy
Kontener `#routeCard` ma trzy atrybuty z zasięgiem linii (0–100, od Węglina do Abramowic):

```html
data-progress-red="90"      <!-- odcinek z utworzonymi ulicami -->
data-progress-orange="60"   <!-- odcinek do renowacji -->
data-progress-green="30"    <!-- ukończony odcinek -->
```

Linie mają tę samą grubość i leżą jedna na drugiej: czerwona (na dole), pomarańczowa, zielona (na wierzchu). Każda kolejna przykrywa poprzednią, więc zwykle `czerwona ≥ pomarańczowa ≥ zielona`.

Podkład to `image/Przebiegtrasy.png` o proporcjach 1376×752. Jeśli podmieniasz obraz, zachowaj te proporcje, bo ścieżki SVG mają współrzędne w układzie `viewBox="0 0 1376 752"`. Punkty WĘGLIN i ABRAMOWICE są stałe.

## 7. Galeria

Każde zdjęcie to `<figure class="card">` w `.gallery` z obrazem, tytułem (`h3`) i opisem (`p`). Kliknięcie otwiera powiększenie (lightbox); zamyka się klikając w dowolnym miejscu.

## 8. Info

- Mapa Google to `<iframe>` w `.map`. Jeśli masz klucz Google Maps Embed API, możesz zmienić `src` na `https://maps.google.com/maps?q=Lublin,%20Polska&z=12&output=embed`.
- Statystyki miasta edytujesz w blokach `.stat`.
- Cztery zdjęcia w `.photos` – podmieniasz ścieżki w `src`. Gdy obrazu brakuje, pokazuje się placeholder.

## 9. Motyw

Domyślny motyw to ciemny (`data-theme="dark"`), przełączany na jasny przyciskiem w nagłówku. Wybór zapisuje się w `localStorage` pod kluczem `pl-theme`. Kolory definiują zmienne CSS w `:root` oraz `[data-theme="dark"]` / `[data-theme="light"]`; akcent to `--accent`.

## 10. Meta tagi i SEO

W `<head>` znajdują się:

- `title`, `description`, `keywords`, `author`, `robots`, `theme-color`, `canonical`,
- Open Graph (`og:*`) – podgląd linku na Facebooku, Discordzie, Messengerze,
- Twitter Card (`twitter:*`),
- dane strukturalne JSON-LD (`WebSite`).

**Do zrobienia po wdrożeniu:**

1. Zamień `https://projektlublin.github.io/ProjektLublin/` na prawdziwy adres (w `canonical`, `og:url`, `og:image`, `twitter:image` i JSON-LD).
2. Obraz `og:image` powinien mieć bezwzględny adres i najlepiej 1200×630 px (np. `image/og-cover.png`).
3. Dodaj `robots.txt` i `sitemap.xml` w katalogu głównym i zgłoś stronę w Google Search Console.
4. Ponieważ zakładki działają na haszu (`#...`), Google indeksuje wyłącznie jedną stronę. Osobne indeksowanie podstron wymaga rozdzielenia ich na osobne pliki HTML.

## 11. Bezpieczeństwo

- Polityka CSP (`<meta http-equiv="Content-Security-Policy">`) ogranicza źródła skryptów, obrazów, stylów i ramek. Dozwolone zewnętrzne zasoby: Google Fonts oraz osadzenia Google Maps. Jeśli dodasz nowe źródło (np. osadzone wideo), dopisz je do odpowiedniej dyrektywy.
- Skrypt na początku `<head>` chroni przed osadzeniem strony w cudzej ramce (clickjacking). Najlepiej dodatkowo ustawić nagłówki HTTP (`Content-Security-Policy`, `X-Frame-Options` lub `frame-ancestors`) po stronie hostingu.
- Linki zewnętrzne mają `rel="noopener noreferrer"`.
- Dane z `mods.json` są walidowane (długości, dozwolone znaki, tylko `https://` w linkach, tylko lokalne pliki multimedialne).

## 12. Znane uwagi

- Obrazy w atrybutach `width`/`height` mają wartość `2013×48` (np. miniatura wpisu i galeria). Wyświetlanie jest kontrolowane stylami (`object-fit: cover`), ale warto poprawić atrybuty na rzeczywiste wymiary plików, aby uniknąć skoków układu.
- Pusty `<li></li>` w liście „Do zrobienia” jest liczony do licznika.
- Strona wymaga serwowania przez HTTP(S) (np. hosting lub lokalny serwer), bo `mods.json` jest pobierany przez `fetch`; po otwarciu pliku bezpośrednio z dysku (`file://`) lista modów się nie wczyta.
