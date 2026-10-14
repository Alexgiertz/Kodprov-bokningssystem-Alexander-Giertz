
# Booking System

En konsolapplikation skriven i C# för att hantera bokningar av mötesrum.

## Funktioner

- Visa aktiva bokningar, sorterade efter datum och starttid.
- Söka efter aktiva bokningar med hela eller delar av kundnamnet, oavsett stora eller små bokstäver.
- Skapa bokningar med kundnamn, rum, datum, starttid, sluttid och antal deltagare.
- Avboka bokningar med bokningsnummer. Avbokade bokningar sparas men visas inte bland aktiva bokningar eller sökresultat.
- Validera kundnamn, rum, tider och antal deltagare samt förhindra överlappande bokningar i samma rum på samma datum.

## Mötesrum

| Rum | Max antal deltagare |
| --- | --- |
| Aspen | 4 |
| Birken | 8 |
| Eken | 12 |

## Krav

- .NET 10 SDK

## Starta programmet

Kör följande kommando från projektets rotmapp, där `BookingSystemExam.slnx` finns:

```bash
dotnet run --project src/BookingSystem
```

Följ menyn för att visa, söka, skapa eller avboka bokningar. Välj `0` för att avsluta.
Datum anges som `yyyy-MM-dd` och tider som `HH:mm`.

## Köra tester

Kör från projektets rotmapp:

```bash
dotnet test
```

Testerna använder xUnit och en repository som lagrar bokningar i minnet.
I Visual Studio kan testerna även köras via **Test > Test Explorer > Run All Tests**.

## Datalagring

Bokningar sparas i en JSON-fil. Filen `src/BookingSystem/Data/bookings.json` kopieras till programmets utdatamapp vid byggning.
Programmet läser och sparar sedan kopian i `Data/bookings.json` under den körande applikationens mapp, vanligtvis:

```text
src/BookingSystem/bin/Debug/net10.0/Data/bookings.json
```

Nya bokningar och avbokningar finns kvar mellan körningar. Källfilen uppdateras inte när programmet sparar.
Om utdatamappen rensas kan sparade bokningar försvinna. En nyare källfil kan också ersätta kopian vid byggning.

## Projektstruktur

- `src/BookingSystem/ConsoleUI` – menyer, inmatning och visning av bokningar.
- `src/BookingSystem/Services` – bokningslogik och validering.
- `src/BookingSystem/Models` – datamodeller.
- `src/BookingSystem/Repositories` – läsning och sparande av bokningar.
- `src/BookingSystem/Data` – JSON-filen som kopieras vid byggning.
- `src/BookingSystem/Docs` – dokumentation.
- `tests/BookingSystem.Tests` – enhetstester och repository för testning.
