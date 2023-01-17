# postman-testing-student-api

### Exempel kod på hur du kopplar MongoDB till Spring Boot och gör enkla queries (CRUD).

## API

#### POST - Skapa en student
http://localhost:8080/student

Börja med att skapa ett par studenter genom att använda tex. Postman. Du kan kopiera eller skapa en student enligt följande:

{ 
    "name": "Exempel", 
    "email": "exempel@klarr.se", 
    "department": { 
    "departmentName": "Computer Science", 
    "location": "SWEDEN" 
    }, 
    "subjects": [ {
    "subjectName": "Java", 
    "pointsObtained": 60}, 
    {"subjectName": "JavaScript",
    "pointsObtained": 90} ] 
}

Se till att göra en POST request och skicka med bodyn som JSON raw.

### GET - Student med exakt id
http://localhost:8080/student/{id}

Kopiera en students id, OBS! endast id inte hela ObjectId. Byt ut {id} till det id du kopierade och gör en GET request.
getById() är en metod som vi får av MongoDB kommer att returnera ett objekt ifrån från student collection, alltså den student som matchar med det id som vi skickar med.

### GET - Hämta alla studenter
http://localhost:8080/students

Hämta alla document i vår Student collection. Vi använder List här i vår controller och vår service metod för att kunna returnera mer än ett dokument från vår
collection. Om du har extremt myclet dokument i din databas bör du ta dig en funderare på hur du använder findAll() metoden, du skulle säkerligen behöva skriva lite mer kod.
findAll() får vi från MongoDB.

### PUT - Uppdatera en student
http://localhost:8080/update

Här lägger vi vår unika identifier i vår request body och inte direkt i vår URL. Vi hade kunnat lägga den i vår URL likt getById() men då hade vi inte fått
möjligheten att faktiskt skapa en ny student om ingen unik identifier bifogas. Så det här är egentligen spara eller uppdatera berodende på om vi skickar  med 
något id eller inte i vår request body. 
Exempel:

{

    "id": **"kopiera id från din databas och klistra in här"**,
   "name": "Exempel UPPDATERAD", <--- ändra något tex lägg till text
    "email": "exempel@klarr.se", 
    "department": { 
    "departmentName": "Computer Science", 
    "location": "SWEDEN" 
    }, 
    "subjects": [ {
    "subjectName": "Java", 
    "pointsObtained": 60}, 
    {"subjectName": "JavaScript",
    "pointsObtained": 90} ] 
}

### DELETE - Ta bort en student
http://localhost:8080/delete/{id}

Här gör vi precis som i findById(), vi lägger till det unika id för vår student som vi vill ta bort i vår URL. Vi lägger id i URL path alltså => @PathVariable
Vi returnerar ett meddelande istället för att returnera den borttagna studenten.

### GET - Dokument med exakt fält
http://localhost:8080/studentsByName/{name}

**Innan du går vidare så skapa två studenter med samma namn i din databas.**
studentsByName kommer att ge oss exakt de dokument som matchar med vår @PathVariable. Det är en array med Students eftersom det kan vara så att vi har fler än
en student med samma namn så måste viu returnera en array annars skulle vi bara kunna returnera ett dokument från vår databas.
Spring har "method proxy" så du kan testa att skriva i ditt interface: findBy + ctrl + space och du komme se en lista med fält ifrån vår entity Student klass.
Det här är alltså inte native MongoDB utan vi skapar dessa metoder med hjälp av MongoDB poch utefter de fält vi har i vår collection model (Student). Du kan behöva importera java.utils 
för att det ska fungera med proxyn. Se till att det blir rätt fält som du använder. 
Det här möjligör att vi kan skapa helt egna metoder för våra collections i vår databas.

Byt ut {name} till de namn du har på två studenter i din databas och testa din request.

### AND operator med flera fält
### GET - studenter med fält name OCH fält email
http://localhost:8080/studentsByNameAndMail

Precis som studentsByName är det här inte native MongoDB utan med hjälp av MongoDB proxyn kan vi skapa våra egna metoder som är kopplade till våra unika
fält. **OBS! Var noga med att ange korrekt fält i ditt interface! Nedanstående kommer att ge dig error pga String name kommer att kopplas ihop med fältet email**

`List <Student> findByEmailAndName (String name, String email);`

**email måste alltså vara före name för att det här ska fungera:**
`List <Student> findByEmailAndName (String email, String name);`
  
Vi använder List här eftersom vi vill kunna returnera fler än ett dokument från vår databas. Om vi tar bort List kommer vi bara kunna att returnera ett dokument
  från databasen. Testa gärna men glöm inte att ändra repository, service och controller.
    
### OR operator med flera fält
### GET - studenter med fält name ELLER fält email
http://localhost:8080/studentsByNameOrMail

Precis samma gäller här som för AND exemplet. Se till att du  returnerar en lista eftersom vi kan hitta flera dokument med dessa fält. Samma gäller ordningen i ditt interface, här behöver alltså String name vara först. 
  

