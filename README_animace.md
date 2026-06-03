# Přehled efektů a animací (README)

Tento dokument shrnuje všechny interaktivní grafické efekty, animace a chování implementované v projektu **Carp scale**.

---

## 1. Interaktivní 3D Parallax efekt (Tilt)
Dekorativní květinové motivy na pozadí (`kytka.svg`) dynamicky reagují na pohyb kurzoru nebo naklánění mobilního zařízení.

*   **Princip:** 
    *   Květinový motiv je rozdělen do dvou samostatných obalů (`.bg-line-left-wrap` a `.bg-line-right-wrap`) umístěných vedle sebe (levý zabírá `left: 5%`, pravý `left: 45%` s šířkou `50%` každého obalu).
    *   Každý obal registruje pohyb myši **nezávisle na druhém** a pouze tehdy, nachází-li se kurzor přímo v jeho detekční oblasti (`getBoundingClientRect`).
    *   Výsledné úhly rotace se ukládají do CSS proměnných `--mouse-rot-x-left`, `--mouse-rot-y-left`, atd., které otáčejí prvky ve 3D prostoru.
*   **Odezva:** Obě poloviny mají nastavenu shodnou odezvu s koeficientem **`1.4`**.
*   **Plynulý návrat:** Pokud myš opustí detekční zónu jednoho z obalů, daný motiv se plynule vrátí do výchozího stavu (`0deg`) rychlostí definovanou v konfiguraci (`returnLeaveSpeed`).
*   **Podpora gyroskopu (Mobile):** Na zařízeních podporujících `DeviceOrientationEvent` (po schválení oprávnění v prohlížeči) se 3D rotace ovládá nakláněním telefonu (změna úhlů `beta` a `gamma`).
*   **Reakce na kliknutí na šipky (Click Tilt Response):**
    *   Při kliknutí na navigační šipku se dočasně aktivuje prudší jednorázový náklon pozadí **pouze na motivu, který je na straně kliknuté šipky** (levá šipka nakloní levou kytku, pravá šipka nakloní pravou kytku).
    *   Tento náklon (`tiltOffsetYLeft` / `tiltOffsetYRight`) se vypočítá na základě hodnoty `config.tiltAngle * 1.2`.
    *   Náklon trvá po dobu nastavenou v `config.tiltDuration` (v milisekundách) a poté se motiv plynule vrátí do středu rychlostí definovanou v `returnClickSpeed`.

---

## 2. Přepínání fotografií (Slideshow bez prolínání)
Změna snímků v centrálním rámečku při kliknutí na navigační šipky.

*   **Princip:**
    *   Rámeček obsahuje jeden element `.center-photo`. Při kliknutí na šipku se okamžitě přepne hodnota `background-image` (cyklicky prochází pole `photos` ['kapr_foto1.jpg', 'kapr_foto2.jpg']).
    *   Tato změna proběhne bez jakéhokoliv plynulého prolnutí (crossfade), které bylo z projektu cíleně odstraněno pro zjednodušení a čistší styl.

---

## 3. Světelný impulz na propojovacích linkách (SVG Line Pulse)
Vizuální zpětná vazba na kliknutí, kdy se světelný paprsek rozběhne po linkách spojujících šipky s okrajem obrazovky.

*   **Princip:** 
    *   Propojovací čáry jsou vykrešleny pomocí SVG (`.horizontal-line-left` a `.horizontal-line-right`) a vybarveny lineárními gradienty (`#grad-left` a `#grad-right`).
    *   JavaScript po kliknutí na šipku animuje posun barevných přechodových bodů (`stop`) pomocí `requestAnimationFrame`.
    *   Animace využívá nelineární časování (ease-out cubic/quartic), kdy světlo vystřelí velmi rychle a na konci linky pozvolna dohasíná.

---

## 4. Pozvolné pulzování na pozadí (Background Pulsing)
Jemný pohyb na pozadí evokující dýchání nebo organický život.

*   **Princip:**
    *   Pokud je v nastavení zapnuto, na pozadí se aplikují CSS animace měřítka (`scale`).
    *   **Levá část:** Animuje se přes `@keyframes pulse-anim-bg-left` s periodou **12 sekund** (měřítko se mění od `1` do `1.08`).
    *   **Pravá část:** Animuje se přes `@keyframes pulse-anim-bg-right` s periodou **14 sekund** (měřítko se mění od `1` do `1.08`).
    *   Rozdílná doba trvání zabraňuje tomu, aby obě části pulzovaly zcela synchronně, což vytváří přirozenější asymetrii.

---

## 5. Upozorňovací animace šipky (Attention Timer Pulse)
Vizuální nápověda pro uživatele, která ho navádí k interakci s webem.

*   **Princip:**
    *   Pokud uživatel po načtení stránky neprovede žádnou akci (neklikne na šipku), po určitém čase (výchozí interval je **10 sekund**) pravá šipka dvakrát pulzně poskočí.
    *   Animace `@keyframes arrow-attention-pulse` zvětší velikost šipky na **`1.35x`** původní velikosti.
    *   Jakmile uživatel na šipku najede kurzorem (`mouseenter`) nebo na ni klikne, tato doprovodná animace se okamžitě a trvale deaktivuje.

---

## 6. Vibrační hmatová odezva (Haptic Feedback)
*   **Princip:** Na mobilních telefonech a zařízeních, která podporují API `navigator.vibrate`, vyvolá kliknutí na navigační šipku krátkou vibraci trvající **25 milisekund** pro fyzické potvrzení kliku.

---

## 7. Skryté vývojářské menu (Debug Menu)
Nástroj pro ladění chování a vzhledu v reálném čase.

*   **Ovládání:** Lze jej otevřít nebo zavřít stisknutím klávesy **`d`** nebo **`D`** na klávesnici (nefunguje, pokud zrovna píšete do textového pole).
*   **Možnosti nastavení:**
    *   Zapnutí/vypnutí pulzování pozadí a 3D rotace.
    *   Nastavení citlivosti, úhlu a doby trvání náklonu po kliknutí, stejně jako maximálního úhlu náklonu podle pohybu myši.
    *   Změna rychlosti návratu motivů při opuštění zóny kurzorem.
    *   Rychlost/délka průběhu světelného impulzu na linkách a rychlost (doba periody) pulzování pozadí.
    *   Interval spuštění upozornění na šipku.
    *   Nastavení tloušťky a délky horizontálních linek.
    *   Změna barvy pozadí webu, barvy šipek (s kurzorem a impulzy) a barvy spodního názvu.
    *   Přepínání textu titulku (např. *Carp scale* vs *Dust*).
