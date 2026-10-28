# ŚCIĄGA X3D – klocki do kopiowania (Lab 1–4)

> Jak używać: znajdź klocek w spisie → skopiuj → zmień liczby/nazwy oznaczone `<<...>>` lub opisane w komentarzu.
> Gotowe pełne projekty do startu: `szablon_projekt.x3dv` (kodowanie klasyczne) i `szablon_x3dom.html` (X3DOM + JS).
> Gotowe POMIESZCZENIE (podłoga, ściany, sufit, drzwi na klik, okno, stół, lampa): `pokoj.x3dv`.
> Zasada ścian: Box jest wyśrodkowany → ściana wys. H stoi na `translation x H/2 z`; otwór na drzwi = 2 kawałki ściany + nadproże.

## SPIS
- **A. Podstawy** – A1 nagłówek pliku · A2 jak składać klocki · A3 kąty w radianach · A4 typy pól
- **B. Bryły** – B1 Box · B2 Sphere · B3 Cone · B4 Cylinder · B5 Text · B6 PointSet · B7 IndexedLineSet · B8 IndexedFaceSet · B9 Extrusion · B10 ElevationGrid (teren)
- **C. Transform** – C1 przesunięcie/obrót/skala · C2 obrót wokół innego punktu
- **D. Wygląd** – D1 Material · D2 tekstura · D3 TextureTransform · D4 gotowe materiały
- **E. Światła** – E1 Directional · E2 Point · E3 Spot
- **F. Kamera** – F1 Viewpoint · F2 NavigationInfo
- **G. Organizacja** – G1 DEF/USE · G2 Group · G3 Switch · G4 Inline · G5 Anchor · G6 PROTO · G7 EXTERNPROTO
- **H. Animacje** – H0 schemat · H1 obrót wokół własnej osi · H2 ruch po ścieżce · H3 orbita · H4 kolor · H5 przezroczystość · H6 pulsowanie (skala) · H7 ruch tam-i-z-powrotem
- **I. Czujniki** – I1 TouchSensor (klik → animacja) · I2 drzwi na zawiasie · I3 PlaneSensor · I4 CylinderSensor · I5 SphereSensor · I6 ProximitySensor · I7 VisibilitySensor · I8 Collision
- **J. Otoczenie** – J1 Background (niebo/ziemia) · J2 panorama · J3 Fog · J4 Sound
- **K. X3DOM (HTML + JS)** – K0 szablon · K1 tłumaczenie klasyczne → XML · K2 animacja X3D w XML · K3 zmiana atrybutu przyciskiem · K4 setInterval (ruch po okręgu) · K5 setTimeout (sygnalizator) · K6 suwak · K7 wybór koloru · K8 checkbox · K9 dodawanie obiektów · K10 zmiana kształtu · K11 ukrywanie (mruganie) · K12 Inline w HTML
- **L. AR (JSARToolKit)** – szablon markera
- **M. Najczęstsze błędy**

---

## A. PODSTAWY

### A1. Nagłówek pliku `.x3dv` (OBOWIĄZKOWY, pierwsza linia)
```vrml
#X3D V3.3 utf8
PROFILE Immersive
# Immersive – pozwala na wszystko z labów (sensory, Fog, Sound, NavigationInfo).
# (W lab1 było też "#VRML V2.0 utf8" – stary VRML, plik .wrl. Na kolosie używaj X3D.)
# Komentarz w pliku zaczyna się od #
```

### A2. Jak składać klocki – drzewo sceny
```
Transform            ← gdzie (translation / rotation / scale)
 └ children [
     Shape           ← obiekt
      ├ appearance Appearance        ← jak wygląda
      │    ├ material Material {...}
      │    └ texture ImageTexture {...}
      └ geometry Box/Sphere/...      ← jaki kształt
   ]
```
**Kolejność w pliku projektu:**
1. nagłówek → 2. NavigationInfo, Viewpointy, Background, Fog, światła → 3. obiekty (z `DEF`) →
4. TimeSensory i interpolatory → 5. **wszystkie `ROUTE` na końcu** (ROUTE musi być PO `DEF` węzłów, których używa).

- `children [ A B C ]` – wiele dzieci w nawiasie kwadratowym; jedno dziecko może być bez `[ ]`.
- Pole pominięte = wartość domyślna (np. `Sphere {}` ma radius 1, `Box {}` ma size 2 2 2).

### A3. Kąty – RADIANY
| stopnie | 30° | 45° | 60° | 90° | 180° | 270° | 360° |
|---|---|---|---|---|---|---|---|
| radiany | 0.52 | 0.785 | 1.047 | **1.57** | **3.14** | 4.71 | **6.28** |

Wzór: `rad = stopnie * 3.14159 / 180`

**Osie:** X → w prawo, Y → w górę, Z → do widza (kamera domyślnie stoi w `0 0 10` i patrzy w −Z).

### A4. Typy pól (żeby wiedzieć co wpisać)
| typ | przykład | znaczenie |
|---|---|---|
| SFFloat | `0.5` | jedna liczba |
| SFVec3f | `1 2 3` | punkt / wektor x y z |
| SFVec2f | `0.5 0.5` | wektor 2D (tekstury) |
| SFRotation | `0 1 0 1.57` | oś x y z + kąt w radianach |
| SFColor | `1 0 0` | kolor R G B (0–1) |
| SFBool | `TRUE` / `FALSE` | WIELKIMI literami |
| SFString | `"tekst"` | |
| MF... | `[ 1 0 0, 0 1 0 ]` | lista (przecinki opcjonalne) |

Kolory: czerwony `1 0 0` · zielony `0 1 0` · niebieski `0 0 1` · żółty `1 1 0` · biały `1 1 1` · czarny `0 0 0` · szary `0.6 0.6 0.6` · pomarańcz `1 0.5 0` · brąz `0.5 0.3 0.1` · fiolet `0.6 0 1` · cyjan `0 1 1`

---

## B. BRYŁY (pole `geometry` w Shape)

```vrml
# B1 PROSTOPADŁOŚCIAN / SZEŚCIAN – size = szerokość(X) wysokość(Y) głębokość(Z)
geometry Box { size 4 4 4 }

# B2 KULA – UWAGA: "średnica 3" → radius 1.5
geometry Sphere { radius 1.5 }

# B3 STOŻEK – "średnica podstawy 2.5" → bottomRadius 1.25 (w lab1 podano bottomRadius jako całość – zależy jak czytasz polecenie)
geometry Cone { bottomRadius 1.25 height 6 }

# B4 WALEC
geometry Cylinder { radius 1 height 3 }
# cienki walec = dysk/pierścień (np. pierścień Saturna):  Cylinder { radius 2.2 height 0.02 }
```

```vrml
# B5 TEKST
Shape {
  appearance Appearance { material Material { diffuseColor 1 1 0 } }
  geometry Text {
    string [ "Imię Nazwisko", "AGH" ]      # każdy napis = osobna linia
    fontStyle FontStyle {
      size 1                               # wysokość liter
      spacing 1.2                          # odstęp między liniami
      justify "MIDDLE"                     # "BEGIN" / "MIDDLE" / "END"
      family "SANS"                        # "SERIF" / "SANS" / "TYPEWRITER"
      style "BOLD"                         # "PLAIN" / "BOLD" / "ITALIC"
    }
  }
}
```

```vrml
# B6 CHMURA PUNKTÓW
Shape {
  appearance Appearance { material Material { emissiveColor 1 1 0 } }  # punkty widać dzięki emissive
  geometry PointSet {
    coord Coordinate { point [ 1 1 0, 2 4 0, 3 5 0, 4 4 0 ] }
  }
}
```

```vrml
# B7 LINIE (łamana) – coordIndex: numery punktów (od 0), -1 = koniec jednej linii
Shape {
  appearance Appearance { material Material { emissiveColor 1 0 0 } }
  geometry IndexedLineSet {
    coord Coordinate { point [ 0 8 0, 0 0 0, 10 0 0 ] }
    coordIndex [ 0, 1, 2, -1 ]
    # opcjonalnie kolor na wierzchołek:
    # colorPerVertex TRUE
    # color Color { color [ 1 1 0, 1 0.3 0.3, 1 0.3 0.3 ] }
  }
}
```

```vrml
# B8 WŁASNA BRYŁA ZE ŚCIAN – np. PIRAMIDA (podstawa kwadrat + 4 trójkąty)
Shape {
  appearance Appearance { material Material { diffuseColor 1 0.8 0 } }
  geometry IndexedFaceSet {
    solid FALSE                       # rysuj obie strony ścian (nie trzeba pilnować kolejności)
    coord Coordinate {
      point [ -1 0 -1,  1 0 -1,  1 0 1,  -1 0 1,   0 2 0 ]   # 0..3 podstawa, 4 = czubek
    }
    coordIndex [
      0 1 2 3 -1      # podstawa
      0 1 4 -1        # boki
      1 2 4 -1
      2 3 4 -1
      3 0 4 -1
    ]
    # kolor na ścianę (zamiast materiału):
    # colorPerVertex FALSE
    # color Color { color [ 0.5 0.5 0.5, 1 0 0, 0 1 0, 0 0 1, 1 1 0 ] }   # 1 kolor na każdą ścianę
  }
}
```

```vrml
# B9 EXTRUSION – przekrój 2D (crossSection, w płaszczyźnie XZ) "wyciągnięty" wzdłuż ścieżki (spine)
Shape {
  appearance Appearance { material Material { diffuseColor 0.2 0.2 0.5 specularColor 0.8 0.2 0.8 } }
  geometry Extrusion {
    crossSection [ 1 1, 1 -1, -1 -1, -1 1, 1 1 ]   # kwadrat (zamknięty – ostatni = pierwszy)
    spine [ 0 0 0, 0 2 0 ]                         # od dołu do góry → graniastosłup
    # scale [ 1 1, 0.1 0.1 ]                       # zwężenie na górze → ostrosłup / ścięty
  }
}
```

```vrml
# B10 TEREN – ElevationGrid. Liczba wartości height = xDimension * zDimension
# Kolejność: najpierw cały rząd X dla z=0, potem rząd dla z=1, itd.
# UWAGA: siatka zaczyna się w 0 0 0 i rośnie w +X i +Z. Rozmiar = (xDimension-1)*xSpacing.
# Żeby była na środku: translation = -(rozmiar/2) 0 -(rozmiar/2). Kamerę ustaw wyżej, patrzącą w dół:
# Viewpoint { position 0 8 14 orientation 1 0 0 -0.5 }
Transform {
  translation -4 0 -4                  # tu: (5-1)*2 = 8 → przesunięcie o -4 (teren na środku)
  children Shape {
    appearance Appearance { material Material { diffuseColor 0.3 0.6 0.2 } }
    geometry ElevationGrid {
      xDimension 5
      zDimension 5
      xSpacing 2.0                     # odległość między punktami w X
      zSpacing 2.0
      creaseAngle 1.57                 # >0 = gładkie cieniowanie
      solid FALSE
      height [
        0 0 0 0 0,
        0 1 2 1 0,
        0 2 4 2 0,
        0 1 2 1 0,
        0 0 0 0 0
      ]
      # kolorowanie wierzchołków: colorPerVertex TRUE + DOKŁADNIE xDim*zDim kolorów (tu 25)
      # colorPerVertex TRUE
      # color Color { color [ ... 25 kolorów ... ] }
    }
  }
}
```

---

## C. TRANSFORM

```vrml
# C1 – kolejność działania: skala → obrót → przesunięcie (niezależnie od kolejności w pliku)
Transform {
  translation 10 15 25          # środek obiektu w punkcie (10,15,25)
  rotation 1 0 0 0.4            # obrót wokół osi X o 0.4 rad  (oś Y: 0 1 0 kąt, oś Z: 0 0 1 kąt)
  scale 1 2 1                   # rozciągnij 2x w Y
  children [
    Shape { geometry Cylinder { } }
  ]
}
```

```vrml
# C2 – obrót wokół innego punktu niż środek obiektu: pole center
Transform {
  center 1 0 0                  # punkt obrotu (we współrzędnych lokalnych)
  rotation 0 1 0 1.57
  children Shape { geometry Box { size 2 2 0.1 } }
}
# Alternatywa (pewniejsza przy animacji – patrz I2 drzwi): Transform-w-Transform
```

Zagnieżdżanie: Transform w Transform = przekształcenia się sumują (dziecko porusza się razem z rodzicem) → na tym opiera się orbita (H3).

---

## D. WYGLĄD

```vrml
# D1 MATERIAŁ – wszystkie pola (wpisz tylko potrzebne)
appearance Appearance {
  material Material {
    diffuseColor 0 0 1          # główny kolor (oświetlony)
    emissiveColor 0 0 1         # "świeci sam" – widać nawet bez światła. "emituje kolor niebieski o najw. jasności" → 0 0 1
    specularColor 1 1 1         # kolor odblasku
    shininess 0.5               # połysk 0–1
    ambientIntensity 0.2        # jasność w cieniu
    transparency 0.6            # przezroczystość: 0 = pełny, 1 = niewidoczny. "60% przezroczysta" → 0.6
  }
}
```

```vrml
# D2 TEKSTURA Z PLIKU (plik obok .x3dv albo ścieżka względna)
appearance Appearance {
  material Material { }                            # z materiałem tekstura reaguje na światło
  texture ImageTexture { url "tekstura.jpg" }      # lub url [ "textures/2k_earth_daymap.jpg" ]
}
```

```vrml
# D3 PRZESUNIĘCIE / OBRÓT / SKALA TEKSTURY
appearance Appearance {
  texture ImageTexture { url "name.jpg" }
  textureTransform TextureTransform {
    translation 0.3 0.2         # przesunięcie tekstury do punktu (0.3, 0.2)
    rotation 0.785              # obrót (rad)
    scale 2 2                   # 2x2 = tekstura powtórzona 2 razy w każdą stronę
    center 0.5 0.5
  }
}
```

```vrml
# D4 GOTOWE MATERIAŁY (z wykładu – jako PROTO, patrz G6)
# Złoto:     ambientIntensity 0.4  diffuseColor 0.22 0.15 0.0  specularColor 0.71 0.70 0.56 shininess 0.16
# Aluminium: ambientIntensity 0.3  diffuseColor 0.30 0.30 0.50 specularColor 0.70 0.70 0.80 shininess 0.10
# Miedź:     ambientIntensity 0.26 diffuseColor 0.30 0.11 0.00 specularColor 0.75 0.33 0.00 shininess 0.08
# Szkło:     diffuseColor 0.8 0.9 1  transparency 0.6 specularColor 1 1 1 shininess 0.9
```

**Wielokrotne użycie wyglądu:** `appearance DEF Sciana Appearance { ... }` a dalej `appearance USE Sciana`.

---

## E. ŚWIATŁA
Wyłącz latarkę na głowie, żeby było widać efekt swoich świateł: `NavigationInfo { headlight FALSE }` (F2).

```vrml
# E1 KIERUNKOWE (jak słońce – równoległe promienie). Świeci tylko na obiekty w tej samej grupie/pliku.
DirectionalLight {
  direction 1 0 0          # świeci w stronę +X ("kierunek na dodatnią część osi X")
  color 1 0 0              # czerwone
  intensity 0.5            # połowa intensywności
  ambientIntensity 0
  on TRUE
}
```

```vrml
# E2 PUNKTOWE (żarówka – we wszystkie strony)
PointLight {
  location 0 8 0
  color 1 1 1
  intensity 0.8
  radius 30                # zasięg
  attenuation 1 0 0        # słabnięcie z odległością
  on TRUE
}
```

```vrml
# E3 REFLEKTOR / LATARKA
# "niebieskie światło z prawej strony kuli": stoi na +X i świeci w −X
SpotLight {
  location 10 0 0
  direction -1 0 0
  color 0 0 1
  intensity 1
  beamWidth 1.57           # szerokość pełnej jasności
  cutOffAngle 0.785        # kąt, za którym światło znika
  radius 100
  on TRUE
}
```

---

## F. KAMERA

```vrml
# F1 PUNKTY WIDZENIA – pierwszy w pliku = startowy. Przełączanie: menu przeglądarki / PageUp-PageDown
DEF Start Viewpoint {
  description "Start"
  position 0 1.6 16             # 1.6 = wysokość oczu człowieka
}
DEF Gora Viewpoint {
  description "Z góry"
  position 0 35 0
  orientation 1 0 0 -1.57       # patrz w dół
}
DEF Bok Viewpoint {
  description "Z boku"
  position 20 2 0
  orientation 0 1 0 1.57        # stoisz na +X, patrzysz w stronę środka
}
# Kamera "jadąca" za obiektem: włóż Viewpoint DO ŚRODKA animowanego Transform (jak "Za Ziemią" w lab2/zad3)
```

```vrml
# F2 SPOSÓB PORUSZANIA
NavigationInfo {
  type [ "WALK", "ANY" ]         # "EXAMINE" – obracanie modelu, "WALK" – chodzenie z grawitacją, "FLY" – latanie
  headlight FALSE                # wyłącz światło z kamery
  speed 1.5
  avatarSize [ 0.25, 1.6, 0.75 ] # [promień kolizji, wysokość oczu, max wysokość stopnia]
}
```

---

## G. ORGANIZACJA KODU

```vrml
# G1 DEF / USE – zdefiniuj raz, użyj wiele razy.  Np. SCHODY 6-stopniowe:
Transform {
  translation 0 0.5 0
  children [
    DEF Stopien Shape {
      appearance Appearance { material Material { diffuseColor 0.6 0.4 0.2 } }
      geometry Box { size 4 1 2 }
    }
  ]
}
Transform { translation 0 1.5 -2  children [ USE Stopien ] }
Transform { translation 0 2.5 -4  children [ USE Stopien ] }
Transform { translation 0 3.5 -6  children [ USE Stopien ] }
Transform { translation 0 4.5 -8  children [ USE Stopien ] }
Transform { translation 0 5.5 -10 children [ USE Stopien ] }
# Każdy stopień: Y +1 (wysokość), Z −2 (głębokość).
```

```vrml
# G2 GROUP – zgrupowanie bez przekształcania
DEF Grupa Group {
  children [
    Shape { geometry Sphere { } }
    Shape { geometry Cylinder { radius 0.3 height 5 } }
  ]
}
```

```vrml
# G3 SWITCH – pokazuje tylko jedno dziecko (whichChoice = numer od 0; -1 = nic)
DEF Przelacznik Switch {
  whichChoice 0
  children [                       # (w starym VRML zamiast children było: choice)
    Shape { geometry Box { } }
    Shape { geometry Sphere { } }
    Shape { geometry Cone { } }
  ]
}
```

```vrml
# G4 INLINE – wczytaj inny plik jako część sceny
Inline { url [ "auta.x3dv" ] }
# Przesunięty: Transform { translation 5 0 0 children Inline { url "auta.x3dv" } }
```

```vrml
# G5 ANCHOR – klikalny link (strona albo Viewpoint)
Anchor {
  url "#Gora"                      # skok do Viewpointa o DEF Gora   (strona: url "index.html";  inny plik: "inny.x3dv#Widok")
  description "Idź na górę"
  children [ Shape { geometry Box { } } ]
}
```

```vrml
# G6 PROTO – własny węzeł z parametrami (definicja MUSI być przed użyciem)
PROTO Krzeslo [
  field SFColor kolorNogi 1 0 0
  field SFColor kolorSiedziska 0 1 0
]
{
  Transform {
    children [
      Transform {                                   # siedzisko
        translation 0 0.6 0
        children Shape {
          appearance Appearance { material Material { diffuseColor IS kolorSiedziska } }
          geometry Cylinder { height 0.1 radius 1.0 }
        }
      }
      Transform {                                   # noga 1 (DEF), reszta USE
        translation -0.5 0 -0.5
        children DEF Noga Shape {
          appearance Appearance { material Material { diffuseColor IS kolorNogi } }
          geometry Cylinder { height 1 radius 0.1 }
        }
      }
      Transform { translation  0.5 0 -0.5 children USE Noga }
      Transform { translation -0.5 0  0.5 children USE Noga }
      Transform { translation  0.5 0  0.5 children USE Noga }
    ]
  }
}

# użycie:
Krzeslo { }                                          # domyślne kolory
Transform { translation 3 0 0 children Krzeslo { kolorNogi 1 1 0 kolorSiedziska 0 0 1 } }

# Typy parametrów PROTO: SFColor, SFFloat, SFVec3f, SFString, SFBool, SFRotation, SFTime
# Wewnątrz PROTO mogą być TimeSensory, interpolatory i ROUTE (jak pochodnia w lab3) – ROUTE wpisz wewnątrz { } PROTO.
# Prosty PROTO-materiał:  PROTO MojKolor [ ] { Material { diffuseColor 1 1 0 } }   → material MojKolor { }
```

```vrml
# G7 EXTERNPROTO – PROTO z innego pliku
EXTERNPROTO Miedz [ ] "mojaBiblioteka.x3dv#Miedz"
Shape { appearance Appearance { material Miedz { } } geometry Box { size 10 3 0.5 } }
```

---

## H. ANIMACJE

### H0. Schemat każdej animacji (3 kroki)
```
TimeSensor ──fraction_changed──► Interpolator.set_fraction
Interpolator ──value_changed──► Obiekt.set_POLE
```
| chcę zmieniać | interpolator | keyValue | ROUTE do |
|---|---|---|---|
| położenie | `PositionInterpolator` | `x y z` | `Transform.set_translation` |
| obrót | `OrientationInterpolator` | `ox oy oz kąt` | `Transform.set_rotation` |
| skalę | `PositionInterpolator` | `sx sy sz` | `Transform.set_scale` |
| kolor | `ColorInterpolator` | `r g b` | `Material.set_diffuseColor` / `set_emissiveColor` / `PointLight.set_color` |
| jedną liczbę | `ScalarInterpolator` | `0.5` | `Material.set_transparency`, `PointLight.set_intensity` |
| kształt | `CoordinateInterpolator` | wiele punktów na klucz | `Coordinate.set_point` |

Zasady: **liczba `key` = liczba `keyValue`**; `key` od 0 do 1 rosnąco; obiekt docelowy musi mieć `DEF`.
TimeSensor: `cycleInterval` = czas cyklu w s; `loop TRUE` = od razu i w kółko; `loop FALSE` = czeka na `set_startTime` (klik/czujnik).

```vrml
# H1 OBRÓT WOKÓŁ WŁASNEJ OSI (Y) – w kółko
DEF Kostka Transform {
  children Shape {
    appearance Appearance { material Material { diffuseColor 0 0 1 } }
    geometry Box { }
  }
}
DEF Zegar TimeSensor { loop TRUE cycleInterval 4 }
DEF Obrot OrientationInterpolator {
  key      [ 0, 0.25, 0.5, 0.75, 1 ]
  keyValue [ 0 1 0 0, 0 1 0 1.57, 0 1 0 3.14, 0 1 0 4.71, 0 1 0 6.28 ]   # (zawsze 5 kroków, z 2 kluczami 0→6.28 nie działa!)
}
ROUTE Zegar.fraction_changed TO Obrot.set_fraction
ROUTE Obrot.value_changed TO Kostka.set_rotation
```

```vrml
# H2 RUCH PO ŚCIEŻCE (np. kwadrat/okrąg dookoła środka) – pierwszy punkt = ostatni → płynna pętla
DEF Kula Transform {
  translation 5 0 0
  children Shape {
    appearance Appearance { material Material { diffuseColor 1 1 0 } }
    geometry Sphere { }
  }
}
DEF Czas TimeSensor { loop TRUE cycleInterval 10 }
DEF Sciezka PositionInterpolator {
  key      [ 0, 0.25, 0.5, 0.75, 1 ]
  keyValue [ 5 0 0, 0 0 -5, -5 0 0, 0 0 5, 5 0 0 ]       # wokół osi Y (poziomo). Pionowo: 5 0 0, 0 5 0, -5 0 0, 0 -5 0, 5 0 0
}
ROUTE Czas.fraction_changed TO Sciezka.set_fraction
ROUTE Sciezka.value_changed TO Kula.set_translation
```

```vrml
# H3 ORBITA (idealny okrąg, jak planeta) – obracasz RODZICA, dziecko jest odsunięte
DEF Orbita Transform {                 # ← ten się obraca
  children [
    Transform {
      translation 8 0 0                # promień orbity
      children [
        DEF Planeta Transform {        # ← ten może się dodatkowo obracać wokół siebie (H1)
          children Shape {
            appearance Appearance { material Material { diffuseColor 0 0.5 1 } }
            geometry Sphere { radius 0.6 }
          }
        }
        # KSIĘŻYC: wstaw tu kolejny "DEF OrbitaKsiezyca Transform { children Transform { translation 1.2 0 0 ... } }"
      ]
    }
  ]
}
DEF CzasOrbity TimeSensor { loop TRUE cycleInterval 10 }
DEF RuchOrbity OrientationInterpolator {
  key      [ 0, 0.25, 0.5, 0.75, 1 ]
  keyValue [ 0 1 0 0, 0 1 0 1.57, 0 1 0 3.14, 0 1 0 4.71, 0 1 0 6.28 ]
}
ROUTE CzasOrbity.fraction_changed TO RuchOrbity.set_fraction
ROUTE RuchOrbity.value_changed TO Orbita.set_rotation
# Jeden interpolator może sterować wieloma obiektami (kilka ROUTE z tego samego value_changed).
# Różne prędkości = osobne TimeSensory z innym cycleInterval.
```

```vrml
# H4 ZMIANA KOLORU (np. płomień czerwony → żółty → czerwony)
Shape {
  appearance Appearance {
    material DEF KolorPlomienia Material { diffuseColor 1 0 0 emissiveColor 0.8 0.2 0 }
  }
  geometry Cone { bottomRadius 0.2 height 0.5 }
}
DEF CzasKoloru TimeSensor { loop TRUE cycleInterval 2 }
DEF Plomien ColorInterpolator {
  key      [ 0, 0.5, 1 ]
  keyValue [ 1 0 0, 1 1 0, 1 0 0 ]
}
ROUTE CzasKoloru.fraction_changed TO Plomien.set_fraction
ROUTE Plomien.value_changed TO KolorPlomienia.set_diffuseColor
ROUTE Plomien.value_changed TO KolorPlomienia.set_emissiveColor   # mocniej widać
```

```vrml
# H5 ZNIKANIE / POJAWIANIE SIĘ (przezroczystość) – ScalarInterpolator
Shape {
  appearance Appearance { material DEF MatDuszka Material { diffuseColor 0.8 0.9 1 transparency 0 } }
  geometry Sphere { }
}
DEF CzasZnikania TimeSensor { loop TRUE cycleInterval 3 }
DEF Znikanie ScalarInterpolator {
  key      [ 0, 0.5, 1 ]
  keyValue [ 0, 1, 0 ]           # widoczny → niewidoczny → widoczny
}
ROUTE CzasZnikania.fraction_changed TO Znikanie.set_fraction
ROUTE Znikanie.value_changed TO MatDuszka.set_transparency
```

```vrml
# H6 PULSOWANIE (skala) – PositionInterpolator do pola scale
DEF Serce Transform { children Shape { geometry Sphere { } } }
DEF CzasPuls TimeSensor { loop TRUE cycleInterval 1 }
DEF Puls PositionInterpolator {
  key      [ 0, 0.5, 1 ]
  keyValue [ 1 1 1, 1.5 1.5 1.5, 1 1 1 ]
}
ROUTE CzasPuls.fraction_changed TO Puls.set_fraction
ROUTE Puls.value_changed TO Serce.set_scale
```

```vrml
# H7 TAM I Z POWROTEM (winda, wahadło) – ostatni klucz wraca do pierwszego
DEF Winda Transform { children Shape { geometry Box { size 2 0.2 2 } } }
DEF CzasWindy TimeSensor { loop TRUE cycleInterval 6 }
DEF RuchWindy PositionInterpolator {
  key      [ 0, 0.4, 0.5, 0.9, 1 ]
  keyValue [ 0 0 0, 0 5 0, 0 5 0, 0 0 0, 0 0 0 ]   # w górę, postój, w dół, postój
}
ROUTE CzasWindy.fraction_changed TO RuchWindy.set_fraction
ROUTE RuchWindy.value_changed TO Winda.set_translation

# WAHADŁO: OrientationInterpolator keyValue [ 0 0 1 -0.5, 0 0 1 0.5, 0 0 1 -0.5 ] + punkt zawieszenia jak w I2
```

---

## I. CZUJNIKI (interakcja)

```vrml
# I1 KLIKNIĘCIE URUCHAMIA ANIMACJĘ
# TouchSensor reaguje na kliknięcie RODZEŃSTWA (Shape'ów w tym samym children).
Transform {
  children [
    DEF Klik TouchSensor { }
    Shape {
      appearance Appearance { material Material { diffuseColor 1 0 0 } }
      geometry Box { }
    }
  ]
}
DEF CzasKlik TimeSensor { loop FALSE cycleInterval 2 }       # loop FALSE = czeka na klik
ROUTE Klik.touchTime TO CzasKlik.set_startTime
# ... dalej zwykła animacja: CzasKlik.fraction_changed → Interpolator → obiekt
# Przycisk w innym miejscu niż obiekt: TouchSensor przy przycisku, animacja na innym obiekcie (jak w lab3)
# Inne zdarzenia TouchSensor: isOver (najechanie myszą, SFBool), isActive (wciśnięty), hitPoint_changed
```

```vrml
# I2 DRZWI NA ZAWIASIE (otwierane kliknięciem)
# Zewnętrzny Transform stoi W ZAWIASIE i to on się obraca; wewnętrzny przesuwa drzwi o połowę szerokości.
DEF Drzwi Transform {
  translation -1 1 10                  # położenie zawiasu (lewa krawędź drzwi)
  children [
    Transform {
      translation 1 0 0                # = szerokość drzwi / 2
      children [
        DEF KlikDrzwi TouchSensor { }
        Shape {
          appearance Appearance { material Material { diffuseColor 0.5 0.3 0.1 } }
          geometry Box { size 2 2 0.1 }
        }
      ]
    }
  ]
}
DEF CzasDrzwi TimeSensor { loop FALSE cycleInterval 2 }
DEF Otwieranie OrientationInterpolator {
  key      [ 0, 1 ]
  keyValue [ 0 1 0 0, 0 1 0 1.57 ]     # otwarcie o 90°.  Otwórz i zamknij: key [0,0.5,1] keyValue [0 1 0 0, 0 1 0 1.57, 0 1 0 0]
}
ROUTE KlikDrzwi.touchTime TO CzasDrzwi.set_startTime
ROUTE CzasDrzwi.fraction_changed TO Otwieranie.set_fraction
ROUTE Otwieranie.value_changed TO Drzwi.set_rotation
```

```vrml
# I3 PRZECIĄGANIE MYSZĄ (PlaneSensor – ruch w XY)
Group {
  children [
    DEF Przeciagnij PlaneSensor {
      autoOffset TRUE               # pamiętaj gdzie zostawiłem
      minPosition -5 -5             # granice X Y   (domyślnie bez ograniczeń)
      maxPosition 5 5
    }
    DEF Przesuwany Transform {
      children Shape { appearance Appearance { material Material { diffuseColor 0 0 1 } } geometry Box { size 3 3 0.1 } }
    }
  ]
}
ROUTE Przeciagnij.translation_changed TO Przesuwany.set_translation
```

```vrml
# I4 OBRACANIE MYSZĄ WOKÓŁ Y (CylinderSensor) – np. pokrętło, karuzela
Group {
  children [
    DEF Pokretlo CylinderSensor { autoOffset TRUE minAngle -1 maxAngle 1 }   # bez min/max = bez limitu
    DEF Obracany Transform { children Shape { geometry Box { size 3 3 0.1 } } }
  ]
}
ROUTE Pokretlo.rotation_changed TO Obracany.set_rotation
```

```vrml
# I5 OBRACANIE MYSZĄ WE WSZYSTKIE STRONY (SphereSensor)
Group {
  children [
    DEF Kulka SphereSensor { autoOffset TRUE }
    DEF ObracanyWszedzie Transform { children Shape { geometry Cone { height 4 } } }
  ]
}
ROUTE Kulka.rotation_changed TO ObracanyWszedzie.set_rotation
```

```vrml
# I6 WEJŚCIE W OBSZAR (ProximitySensor) uruchamia animację – np. duszek
DEF Strefa ProximitySensor {
  center 0 1 8                  # środek niewidzialnego pudełka
  size 4 2 4                    # jego rozmiar
}
DEF CzasStrefy TimeSensor { loop FALSE cycleInterval 3 }
ROUTE Strefa.enterTime TO CzasStrefy.set_startTime      # wejście = start
# ROUTE Strefa.isActive TO CzasStrefy.set_loop          # (opcja) zapętlaj dopóki jesteś w środku
# ROUTE Strefa.exitTime TO InnyCzas.set_startTime       # wyjście = inna animacja
# ... CzasStrefy.fraction_changed → PositionInterpolator (ruch duszka) + ScalarInterpolator (znikanie, H5)
# Żeby było widać strefę: wstaw półprzezroczysty Box o tym samym rozmiarze w Collision { enabled FALSE } (I8)
```

```vrml
# I7 OBIEKT WIDOCZNY NA EKRANIE (VisibilitySensor) – np. zmiana koloru gdy na niego spojrzysz
DEF Widzi VisibilitySensor { center 0 0 0 size 2 2 2 }
DEF CzasWidzi TimeSensor { cycleInterval 1 }
ROUTE Widzi.enterTime TO CzasWidzi.set_startTime
# + ColorInterpolator → Material.set_diffuseColor   (exitTime – gdy przestanie być widoczny)
```

```vrml
# I8 KOLIZJE – domyślnie przez ściany nie przejdziesz (tryb WALK). Żeby przez coś PRZECHODZIĆ:
Collision {
  enabled FALSE                         # (w VRML97: collide FALSE)
  children [
    Shape {
      appearance Appearance { material Material { diffuseColor 0.8 0.9 1 transparency 0.5 } }
      geometry Box { size 4 2 4 }
    }
  ]
}
# Zdarzenie przy zderzeniu: DEF Sciana Collision { children [...] }  →  ROUTE Sciana.collideTime TO Czas.set_startTime
# Niewidzialna ściana: Collision { proxy Shape { geometry Box { size 10 3 0.1 } } }
```

---

## J. OTOCZENIE

```vrml
# J1 NIEBO + ZIEMIA (gradient). skyColor ma o 1 kolor WIĘCEJ niż skyAngle (pierwszy = zenit)
Background {
  skyColor    [ 0.0 0.2 0.7, 0.0 0.5 1.0, 1.0 1.0 1.0 ]
  skyAngle    [ 1.309, 1.57 ]
  groundColor [ 0.1 0.1 0.0, 0.4 0.25 0.2, 0.6 0.6 0.6 ]   # pierwszy = nadir (pod stopami)
  groundAngle [ 1.309, 1.57 ]
}
# Kosmos: Background { skyColor 0 0 0.05 }
```

```vrml
# J2 PANORAMA – 6 zdjęć na ściany wielkiego sześcianu
Background {
  frontUrl "front.jpg"  backUrl "back.jpg"
  leftUrl "left.jpg"    rightUrl "right.jpg"
  topUrl "top.jpg"      bottomUrl "bottom.jpg"
}
```

```vrml
# J3 MGŁA – visibilityRange = odległość, na której obiekt całkiem znika
Fog {
  color 1 1 1
  fogType "EXPONENTIAL"          # albo "LINEAR"
  visibilityRange 30
}
# Kolor mgły = kolor tła wygląda najlepiej.
```

```vrml
# J4 DŹWIĘK (przy obiekcie; maxFront/maxBack = gdzie cichnie do zera)
Sound {
  location 0 0 0
  minFront 5  maxFront 30
  minBack 5   maxBack 30
  intensity 1
  spatialize TRUE
  source DEF Muzyka AudioClip { url "dzwiek.wav" loop TRUE }
}
# Dźwięk po kliknięciu: loop FALSE i ROUTE Klik.touchTime TO Muzyka.set_startTime
```

---

## K. X3DOM (X3D w HTML + JavaScript)

### K0. Szablon strony
```html
<html>
<head>
  <meta charset="utf-8">
  <script src="https://www.x3dom.org/download/x3dom.js"></script>
  <link rel="stylesheet" href="https://www.x3dom.org/download/x3dom.css">
</head>
<body>
  <x3d width="800px" height="600px">
    <scene>
      <viewpoint position="0 0 20"></viewpoint>

      <transform id="obiekt" translation="0 0 0">
        <shape>
          <appearance>
            <material id="mat" diffuseColor="1 0 0"></material>
          </appearance>
          <box size="2 2 2"></box>
        </shape>
      </transform>

    </scene>
  </x3d>

  <button onclick="funkcja()">Kliknij</button>

  <script>
    function funkcja() {
      document.getElementById('mat').setAttribute('diffuseColor', '0 0 1');
    }
  </script>
</body>
</html>
```
Tekstury z pliku lokalnego mogą się nie wczytać po dwukliku w plik (blokada przeglądarki) → uruchom serwer w folderze: `python -m http.server` i wejdź na `http://localhost:8000`. Wbudowane w HTML (bez plików) zawsze działa.

### K1. Tłumaczenie: klasyczne `.x3dv` → XML/X3DOM
| klasyczne | XML (X3DOM) |
|---|---|
| `Transform { translation 1 2 3 children [ ... ] }` | `<transform translation="1 2 3"> ... </transform>` |
| `Box { size 2 2 2 }` | `<box size="2 2 2"></box>` |
| `DEF Kula Transform {...}` | `<transform DEF="Kula">` (do ROUTE) + `id="kula"` (do JS) |
| `USE Kula` | `<transform USE="Kula"></transform>` |
| `string [ "Ala" "kot" ]` | `<text string='"Ala" "kot"'>` |
| `url "a.jpg"` | `<ImageTexture url="a.jpg">` |
| `ROUTE A.x TO B.y` | `<ROUTE fromNode="A" fromField="x" toNode="B" toField="y"></ROUTE>` |
| `TRUE` | `"true"` |
| `skyColor [ 0 0 1, 1 1 1 ]` | `skyColor="0 0 1, 1 1 1"` |
| `rotation 0 1 0 1.57` | `rotation="0 1 0 1.57"` |
| `Plane` (tylko X3DOM) | `<plane size="60 60" solid="false"></plane>` |

### K2. Animacja X3D wewnątrz HTML (bez JS)
```html
<transform DEF="kostka">
  <shape><appearance><material diffuseColor="1 1 0"></material></appearance><box></box></shape>
</transform>
<timeSensor DEF="czas" cycleInterval="2" loop="true"></timeSensor>
<positionInterpolator DEF="ruch" key="0 0.5 1" keyValue="0 0 0  0 3 0  0 0 0"></positionInterpolator>
<ROUTE fromNode="czas" fromField="fraction_changed" toNode="ruch" toField="set_fraction"></ROUTE>
<ROUTE fromNode="ruch" fromField="value_changed" toNode="kostka" toField="translation"></ROUTE>
<!-- obrót: <orientationInterpolator DEF="obrot" key="0 0.25 0.5 0.75 1"
     keyValue="0 1 0 0, 0 1 0 1.57, 0 1 0 3.14, 0 1 0 4.71, 0 1 0 6.28"> + toField="rotation" -->
```

### K3. Zmiana dowolnego atrybutu przyciskiem
```js
// <material id="mat" ...>, <box id="kostka" ...>, <transform id="obiekt" ...>
function zmienKolor()   { document.getElementById('mat').setAttribute('diffuseColor', '1 1 0'); }
function przezroczysty(){ document.getElementById('mat').setAttribute('transparency', '0.8'); }
function rozmiar()      { document.getElementById('kostka').setAttribute('size', '5 5 5'); }
function przesun()      { document.getElementById('obiekt').setAttribute('translation', '3 0 0'); }
function obroc()        { document.getElementById('obiekt').setAttribute('rotation', '0 1 0 0.785'); }
function skaluj()       { document.getElementById('obiekt').setAttribute('scale', '2 2 2'); }
// Odczyt: el.getAttribute('translation')  → string "3 0 0"  → .split(' ').map(Number) → [3,0,0]
```

### K4. setInterval – animacja w JS (ruch po okręgu / linii / zmiana koloru)
```js
let timer = null;
function startOkrag() {
  if (timer) return;                       // żeby 2. klik nie przyspieszał
  let kat = 0;
  const R = 15;                            // promień
  timer = setInterval(function () {
    kat += 0.02;                           // prędkość
    const x = Math.cos(kat) * R;
    const y = Math.sin(kat) * R;
    document.getElementById('punkt').setAttribute('translation', `${x} ${y} -50`);
  }, 16);                                  // co ile ms (16 ≈ 60 klatek/s)
}
function stop() { clearInterval(timer); timer = null; }

// ruch po linii:   x += 0.1;  setAttribute('translation', `${x} 0 -50`)
// zmiana koloru:   k = (k + 0.05) % 1;  setAttribute('diffuseColor', `${k} 0 ${1 - k}`)
// obrót:           kat += 0.05; setAttribute('rotation', `0 1 0 ${kat}`)
// start sam po załadowaniu: window.onload = function () { startOkrag(); };
```

### K5. setTimeout – kolejne etapy z czasem (SYGNALIZATOR)
```html
<!-- 3 kule z id="czerwone", "zolte", "zielone" (material, emissiveColor="0 0 0" = zgaszone) -->
<script>
  const kolory = { czerwone: '1 0 0', zolte: '1 1 0', zielone: '0 1 0' };
  const fazy = [
    { swiatla: ['czerwone'],          czas: 3000 },
    { swiatla: ['czerwone', 'zolte'], czas: 1000 },
    { swiatla: ['zielone'],           czas: 3000 },
    { swiatla: ['zolte'],             czas: 1000 }
  ];
  function ustawSwiatla(zapalone) {
    for (const nazwa in kolory) {
      const m = document.getElementById(nazwa);
      m.setAttribute('emissiveColor', zapalone.includes(nazwa) ? kolory[nazwa] : '0 0 0');
    }
  }
  let faza = 0;
  function nastepnaFaza() {
    ustawSwiatla(fazy[faza].swiatla);
    setTimeout(nastepnaFaza, fazy[faza].czas);
    faza = (faza + 1) % fazy.length;
  }
  window.onload = nastepnaFaza;
</script>
```

### K6. Suwak (range) → rozmiar / pozycja / przezroczystość
```html
<label>Rozmiar:</label>
<input type="range" id="suwak" min="0.1" max="3" step="0.1" value="1">
<span id="wartosc">1</span>
<script>
  const suwak = document.getElementById('suwak');
  suwak.addEventListener('input', function () {
    document.getElementById('kula').setAttribute('radius', suwak.value);          // <sphere id="kula">
    // skala całej części:  document.getElementById('glowa').setAttribute('scale', `${suwak.value} ${suwak.value} ${suwak.value}`);
    document.getElementById('wartosc').textContent = suwak.value;
  });
</script>
```

### K7. Wybór koloru z palety
```html
<input type="color" id="paleta" value="#0000ff">
<script>
  function hexToRGB(hex) {                     // "#ff8000" → "1 0.5 0"
    const r = parseInt(hex.substring(1, 3), 16) / 255;
    const g = parseInt(hex.substring(3, 5), 16) / 255;
    const b = parseInt(hex.substring(5, 7), 16) / 255;
    return `${r} ${g} ${b}`;
  }
  document.getElementById('paleta').addEventListener('input', function () {
    document.getElementById('mat').setAttribute('diffuseColor', hexToRGB(this.value));
  });
  // losowy kolor: `${Math.random()} ${Math.random()} ${Math.random()}`
</script>
```

### K8. Checkbox – włącz/wyłącz światło (lub cokolwiek)
```html
<spotLight id="latarka" on="true" color="1 1 0" location="0 0 12" cutOffAngle="0.78" beamWidth="0.9" radius="22"></spotLight>
...
<input type="checkbox" checked onclick="przelacz('latarka', this.checked)"> <label>światło</label>
<script>
  function przelacz(id, wlaczone) {
    document.getElementById(id).setAttribute('on', wlaczone ? 'true' : 'false');
  }
</script>
```

### K9. Dodawanie obiektów z JS (np. rysowanie inicjałów kropkami)
```js
function dodajKule(x, y, z, kolor) {
  const t = document.createElement('transform');
  t.setAttribute('translation', `${x} ${y} ${z}`);
  const s = document.createElement('shape');
  const a = document.createElement('appearance');
  const m = document.createElement('material');
  m.setAttribute('diffuseColor', kolor);
  a.appendChild(m);
  s.appendChild(a);
  const g = document.createElement('sphere');
  g.setAttribute('radius', '0.6');
  s.appendChild(g);
  t.appendChild(s);
  document.querySelector('scene').appendChild(t);
}
// Krócej: document.querySelector('scene').insertAdjacentHTML('beforeend',
//   `<transform translation="${x} ${y} ${z}"><shape><appearance><material diffuseColor="${kolor}"></material></appearance><sphere radius="0.6"></sphere></shape></transform>`);

// PUNKTY NA ODCINKU (x1,y1)→(x2,y2), n kroków:
function punktyOdcinka(x1, y1, x2, y2, n) {
  const p = [];
  for (let i = 0; i <= n; i++) {
    const t = i / n;
    p.push([x1 + (x2 - x1) * t, y1 + (y2 - y1) * t, -50]);
  }
  return p;
}
// LITERA = lista odcinków [x1,y1,x2,y2].  M: [[-8,-10,-8,10],[-8,10,0,0],[0,0,8,10],[8,10,8,-10]]
//                                         N: [[12,-10,12,10],[12,10,28,-10],[28,-10,28,10]]
function rysuj(odcinki, kolor) {
  let pkt = [];
  for (const [x1, y1, x2, y2] of odcinki) pkt = pkt.concat(punktyOdcinka(x1, y1, x2, y2, 15));
  let i = 0;
  const id = setInterval(function () {        // po jednej kropce co 50 ms
    if (i >= pkt.length) { clearInterval(id); return; }
    const [x, y, z] = pkt[i++];
    dodajKule(x, y, z, kolor);
  }, 50);
}
// PUNKTY NA OKRĘGU (litera O/C): for (k=0..n) { a = 2*Math.PI*k/n; x = sx + R*Math.cos(a); y = sy + R*Math.sin(a) }
```

### K10. Zmiana kształtu
```js
// <shape id="ksztalt"> <appearance>...</appearance> <box></box> </shape>
function zmienKsztalt() {
  const s = document.getElementById('ksztalt');
  s.querySelector('box, sphere, cone, cylinder').remove();
  s.insertAdjacentHTML('beforeend', '<sphere radius="2"></sphere>');
}
// (wersja z wykładu: s.innerHTML = '<sphere radius="4"/>'  – kasuje też appearance)
// Usuwanie obiektu: document.getElementById('obiekt').remove();
```

### K11. Ukrywanie / mruganie (atrybut render)
```html
<transform id="powieki" render="false"> ... dwa płaskie boxy na oczach ... </transform>
<script>
  function mrugnij() {
    const p = document.getElementById('powieki');
    p.setAttribute('render', 'true');
    setTimeout(() => p.setAttribute('render', 'false'), 150);
  }
  setInterval(mrugnij, 3000);   // samo mruga co 3 s
</script>
<!-- Alternatywa: <switch id="sw" whichChoice="0"> ... </switch>  i  setAttribute('whichChoice', '-1') -->
```

**Minecraftowa postać z prostopadłościanów** (łatwiej edytować niż IndexedFaceSet ze Steve'a):
```html
<transform id="glowa"  translation="0 6 0"><shape><appearance><material id="mGlowa"  diffuseColor="0.9 0.7 0.55"></material></appearance><box size="2 2 2"></box></shape></transform>
<transform id="tulow"  translation="0 3.5 0"><shape><appearance><material id="mTulow"  diffuseColor="0 0.7 0.7"></material></appearance><box size="2 3 1"></box></shape></transform>
<transform id="rekaL"  translation="-1.5 3.5 0"><shape><appearance><material DEF="mRece" id="mRece" diffuseColor="0.9 0.7 0.55"></material></appearance><box size="1 3 1"></box></shape></transform>
<transform id="rekaP"  translation="1.5 3.5 0"><shape><appearance><material USE="mRece"></material></appearance><box size="1 3 1"></box></shape></transform>
<transform id="nogi"   translation="0 1 0"><shape><appearance><material id="mNogi"  diffuseColor="0.2 0.2 0.7"></material></appearance><box size="2 2 1"></box></shape></transform>
<!-- USE="mRece" = ta sama instancja materiału → zmiana koloru mRece zmienia obie ręce.
     Kolor części: setAttribute('diffuseColor', ...) na mGlowa/mTulow/mRece/mNogi (K7).
     Rozmiar części: setAttribute('scale', ...) na glowa/tulow/rekaL/nogi (K6). -->
```

### K12. Wczytanie pliku X3D do HTML
```html
<scene>
  <inline url="model.x3d"></inline>          <!-- plik .x3d (XML). Z Blendera: File → Export → X3D -->
</scene>
```

---

## L. AR – JSARToolKit (szablon z lab4/zad5)
Wklej obiekt w `MatrixTransform` dla wybranego numeru markera (0–99, pliki w `Markers/`). Jednostki są duże (box 80 = mniej więcej wielkość markera).
```html
<Switch id="switcher0" whichChoice="-1">
  <MatrixTransform id="root0">
    <!-- TU TWOJA SCENA dla markera 0 -->
    <transform scale="5 5 5">
      <shape><appearance><material diffuseColor="1 0 0"></material></appearance><box size="10 10 10"></box></shape>
    </transform>
    <transform translation="0 60 0" scale="10 10 10">
      <shape>
        <appearance><material diffuseColor="1 1 1" emissiveColor="1 1 1"></material></appearance>
        <text string='"Imię Nazwisko" "grupa X"' solid="false"><fontStyle family='"SANS"' justify='"MIDDLE" "MIDDLE"'></fontStyle></text>
      </shape>
    </transform>
  </MatrixTransform>
</Switch>
```
W `script.js`: `totalNumberModels = N` → najwyższy używany numer markera (dla markerów 0,1,2 → 2). Każdy marker `i` potrzebuje `switcher{i}` + `root{i}`. Strona musi być na serwerze/https (kamera).

---

## M. NAJCZĘSTSZE BŁĘDY (sprawdź zanim oddasz)
1. Brak `#X3D V3.3 utf8` w PIERWSZEJ linii.
2. `ROUTE` do węzła bez `DEF`, literówka w nazwie (wielkość liter ma znaczenie!) albo ROUTE przed definicją węzła.
3. Liczba `key` ≠ liczba `keyValue`.
4. Obrót 0 → 6.28 z tylko 2 kluczami → obiekt stoi (interpolacja wybiera najkrótszą drogę). Daj 5 kluczy co 1.57.
5. Kąty w stopniach zamiast radianach.
6. Średnica vs promień (`radius` = połowa średnicy).
7. Niezamknięte `{ }` / `[ ]` – licz nawiasy, wcinaj kod.
8. Nie widać światła → `NavigationInfo { headlight FALSE }` i/lub obiekt bez `material` (bez Material obiekt jest biały i nieoświetlany).
9. `TRUE/FALSE` w .x3dv wielkimi literami; w HTML `"true"/"false"`.
10. TouchSensor musi być w tym samym `children` co klikany Shape.
11. Drzwi obracają się wokół środka → zastosuj podwójny Transform (I2).
12. Animacja z kliknięcia nie rusza → TimeSensor musi mieć `loop FALSE` (z `loop TRUE` startuje od razu i kliknięcie nic nie zmienia).
13. X3DOM: w JS używaj `id`, w `ROUTE`/`USE` używaj `DEF`.
14. Tekstura w HTML nie działa po otwarciu z dysku → `python -m http.server`.
