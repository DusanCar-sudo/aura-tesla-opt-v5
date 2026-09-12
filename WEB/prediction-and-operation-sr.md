# Aura TESLA Opt. — predikcija i rad sistema

Ovaj dokument je srpski profesionalni prateći zapis uz V5. Potpuna operativna specifikacija na engleskom je u `prediction-and-operation.md`.

## Misija i cilj uštede

Aura treba da učini potrošnju vidljivom, pomera fleksibilna opterećenja u povoljnije tarifne periode i daje vlasniku objašnjenje za svaku odluku. Predviđeni cilj je **18%–32% niži izbegljivi trošak energije** u pogodnim scenarijima rada. To je modelovana prognoza, ne nezavisno potvrđen rezultat.

## Fizička arhitektura

Energetski sloj koristi ABB DS201 RCBO, sigurnosne DIN terminale, Shelly Pro 3EM i četiri Shelly Pro 1PM modula. CT kleme se postavljaju na tri dolazne faze, strelica je okrenuta ka opterećenju, a PE se nikada ne obuhvata. Svaki izlaz SW1_OUT–SW4_OUT ostaje iza sopstvene zaštite u glavnom razvodu.

Računarski sloj je 24 V DC bus sa Mean Well HDR-100-24 napajanjem, DR-UPS40 UPS modulom, LiFePO4 baterijom i DDR-60G-12 konverterom za lokalni računar i mrežni svič. Računski i energetski vodovi ostaju fizički razdvojeni.

## Satni radni ciklus

Na svakom satu sistem posmatra tarifni prozor, naredna 24 časa, istorijske zbirne podatke, stanje opterećenja i sažetak navika. Lokalni jezički model predlaže strukturirani plan. Deterministička bezbednosna politika zatim proverava tarifni i sigurnosni prozor, poznato opterećenje, nazivnu snagu i format. Tek prihvaćena odluka ulazi u transakciju i izlazni red. Drainer izvršava komandu, ponavlja je do tri puta i vidljivo beleži neuspeh.

## Učenje i revizija

Prekidi korisnika, odobrenja i žalbe čuvaju se kao povratna informacija. Nedeljni skup podataka ima granicu preseka; sirova telemetrija se ne trenira direktno. Adapter prolazi evaluaciona vrata za bezbednost i kvalitet, a prethodna verzija može da se vrati jednom komandom. Svaka odluka ostaje u dnevniku sa razlogom.

## Skaliranje i podaci

Obrazac je skalabilan: dodatni hub može pokrivati zonu, objekat ili proizvodnu liniju, dok nadzorni sloj agregira samo odobrene sažetke. Za industriju su potrebni plan adresiranja, segmentacija mreže, fail-safe stanja, termički proračun, koordinacija zaštite i servisni plan.

Uz privatnu mrežu i bez cloud releja, sirova merenja i upravljački saobraćaj mogu ostati u objektu. To nije automatska garancija privatnosti: proveriti firewall, udaljeni pristup, čuvanje podataka, naloge, logove, bekape i svaku integraciju.

## Ekonomska pretpostavka

Model predviđa pomeranje približno 4–8 kWh dnevno sa vršne tarife od 20–30 ct/kWh u period od 10–14 ct/kWh, uz dodatnu fleksibilnost EV punjenja. Rezultat zavisi od tarifa, navika, ograničenja opterećenja i kvaliteta adaptera. Profesionalac treba da zameni pretpostavke stvarnim merenjima pre investicione odluke.

## Status

Projektna dokumentacija i predikcija ponašanja. Nema tvrdnje o sertifikaciji, garantovanoj uštedi, autonomiji baterije ili spremnosti za terensku instalaciju.
