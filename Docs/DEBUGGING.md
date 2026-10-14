I den ursprungliga versionen av koden som täcker validering av överlappningar:

var hasOverlap = repository.GetAll().Any(existing =>
    !existing.IsCancelled &&
    existing.Date == booking.Date &&
    existing.RoomName.Equals(booking.RoomName, StringComparison.OrdinalIgnoreCase) &&
    booking.StartTime >= existing.StartTime &&
    booking.StartTime < existing.EndTime);

så upptäcktes endast överlappningar om den nya bokningen startade under en befintlig bokning. Detta innebar att följande scenarion inte skulle upptäckas som överlappningar:

- En ny bokning som börjar innan men slutar under en befintlig bokning.
- En ny bokning som börjar tidigare än en befintlig bokning men slutar samtidigt.
- En ny bokning som helt täcker en befintlig bokning (börjar tidigare och slutar senare).

Exempelvis, om det fanns en befintlig bokning från 10:00 till 11:00 och en ny bokning försökte boka från 09:30 till 10:30, skulle detta scenario inte upptäckas som en överlappning 
eftersom den nya bokningen startar innan den befintliga bokningen men slutar under den.

Detta berodde på att villkoren som kontrollerade om en ny bokning överlappade eller inte endast jämförde starttiden för den nya bokningen med start och sluttiden för befintliga bokningar.
I alla dessa scenarion så är booking.StartTime >= existing.StartTime falskt, vilket gör att överlappningen inte upptäcks.

För att åtgärda detta problem ändrade jag villkoren för att inkludera alla möjliga överlappningsscenarion. Den uppdaterade koden ser ut så här:

    var hasOverlap = repository.GetAll().Any(existing =>
        !existing.IsCancelled &&
        existing.Date == booking.Date &&
        existing.RoomName.Equals(booking.RoomName, StringComparison.OrdinalIgnoreCase) &&
        booking.StartTime < existing.EndTime &&
        booking.EndTime > existing.StartTime);

Den kontrollerar nu om den nya bokningen:
- Startar innan en befintlig bokning slutar (booking.StartTime < existing.EndTime)
- Slutar efter en befintlig bokning börjar (booking.EndTime > existing.StartTime)

Om båda dessa villkoren är sanna så betyder det att bokningen överlappar och ska därför inte tillåtas.

Att använda < och > istället för >= innebär även att bokningar som slutar exakt när en annan börjar inte räknas som överlappande.

För att säkerställa att ändringen fungerade korrekt så testade jag att skapa bokningar som överlappar på följande sätt:
- En ny bokning som börjar innan men slutar under en befintlig bokning.
- En ny bokning som börjar tidigare än en befintlig bokning men slutar samtidigt.
- En ny bokning som helt täcker en befintlig bokning (börjar tidigare och slutar senare).

Jag körde även de tester som följde med i repositoriet, och samtliga 14 tester godkändes.

Överlappningskontrollen gäller endast icke avbokade bokningar i samma rum och på samma datum.