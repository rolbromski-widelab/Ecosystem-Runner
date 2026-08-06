# Ecosystem Runner

Endless runner 720 × 300 px, desktop-only. Zbieranie partnerów, omijanie przeszkód, punktacja Partners → Overlaps → Tiers.

## Uruchomienie

Otwórz `index.html` w przeglądarce. Nie wymaga serwera ani zależności.

## Zawartość

```
index.html                     cała gra: HTML + CSS + silnik JS + konfiguracja
assets/
  sky.png            1440×600  statyczne niebo
  horizon.png        1440×600  statyczna sylwetka miasta (z alfą)
  buildings-1.png    1920×200  dalszy plan miasta, parallax 0.08
  buildings-2.png    1920×140  bliższy plan miasta, parallax 0.20
  ground.png          1920×48  podłoże, przewijane 1:1
  character.png     1012×112   postać, 11 klatek po 92×112
  obstacle-s.png     120×288   antena
  obstacle-m.png     168×344   komin mały
  obstacle-l.png     528×592   komin duży
  obstacle-xl.png    350×272   grupa kominów
  flyer.png          368×128   ptak, 2 klatki po 184×128
  partners/                    20 logotypów 160×160
```

## Sterowanie

| Klawisz | Akcja |
|---|---|
| `Spacja` / `↑` | skok (jeden, tylko z ziemi) |
| `↓` | kucnięcie; w powietrzu szybki spadek |
| `P` | pauza |
| `R` | restart |
| `H` | podgląd hitboxów i parametrów fali |

## Konfiguracja

Wszystko, co podmieniasz, jest na **dole `index.html`** w dwóch obiektach:

- `ASSETS` — warstwy tła, podłoże, postać, przeszkody
- `PARTNERS` — lista logotypów do zbierania

Silnik nie wymaga zmian przy podmianie grafik.

### Dodanie partnera

```js
{ id:'nazwa-pliku', name:'Nazwa Wyświetlana', logo:'assets/partners/nazwa-pliku.png' }
```

Logotyp: kwadrat 160 × 160, tło nieprzezroczyste jest OK — silnik przycina go do koła. Talia tasuje się i zapętla, więc liczba partnerów jest dowolna.

## Osadzenie w innej stronie

Gra jest komponentem, nie stroną. Do przeniesienia:

1. Skopiuj blok `<style>` — wszystkie selektory mają prefiks `.xb-`, nie kolidują z Twoim CSS-em.
2. Skopiuj pierwszy blok `<script>` — cały silnik, jedyny globalny symbol to `window.EcosystemRunner`.
3. W docelowej stronie:

```html
<div id="game"></div>
<script>
  EcosystemRunner.mount('#game', { assets: ASSETS, partners: PARTNERS });
</script>
```

Uwagi:

- Silnik nasłuchuje klawiatury na `document`. Jeśli na stronie są pola formularza, listener trzeba zawęzić do kontenera.
- `mount()` zwraca obiekt z metodą `destroy()` — użyj jej, gdy gra jest w modalu, który się zamyka. Bez tego `requestAnimationFrame` kręci się dalej w tle.
- Rekord trzymany jest w `localStorage` pod kluczem `xb_runner_best`, w `try/catch`.

## Parametry, które warto znać

| | |
|---|---|
| Pole gry | 720 × 300 px, renderowane 1:1 |
| Linia podłoża | y = 276 |
| Prędkość | 340 → 760 px/s, sufit po ~32 s |
| Skok | apogeum 141 px, 0,49 s w powietrzu |
| Punktacja | n-ty partner = 10 × n overlapów, × mnożnik tieru |
| Tiery | Explorer 0 · Connector 5 · Supernode 12 (×1,5) · Enterprise 20 (×1,75) · Gravity Node 30 (×2) |

Trudność eskaluje przez losowanie wzorców przeszkód i ich zagęszczanie. Najciaśniejszy wymagany refleks to 132 ms — sprawdzone symulacją 300 tys. fal, nic nie jest niewykonalne.

## Do dopracowania

- Assety tła i przeszkód są w 4–8×, choć kadr renderuje maksymalnie 2×. Eksport w 2× i konwersja do WebP zbiłyby wagę z ~3,7 MB do ok. 400 kB.
- Brak dźwięku.
- Brak obsługi urządzeń dotykowych.
