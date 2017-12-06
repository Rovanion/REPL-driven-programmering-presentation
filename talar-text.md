1. Vad är Clojure? Illustrera att man flyttar parentes och kolon i
   vad_är_clojure.clj.

2. Illustrera varför det är jobbigt att ladda om programmet jämt och
   ständigt med JXWebApp.

   Vi har hittat ett konstigt betéende där det till synes går bra att
   ta bort en inbjudan men den är kvar vid nästa omladdning.

   Vi börjar med att göra en konsollutskrivning där vi tror att felet
   ligger.

   Tomt objekt, happ, vi lägger in en check för det. if (!result || result.error)

   Fortfarande inte löst problemet.

   Ser 404 i serverloggen. Korrigerar felstavning.

   För varje steg måste vi:

   * Vänta på att programmet ska starta om.
   * Ladda om sidan.
   * Sätta mjukvaran i det tillstånd vi önskar.
   * Upprepa det som orsakar felet.




2. Tänk om vi hade ett ännu lite mer komplicerat program och vi ville
   lägga till en sak.

   I stället för att behöva göra ändringen, vänta på att koden
   kompilerar om, ladda om sidan, navigera oss tillbaka. Vore det inte
   bättre om det bara hände?


   Vi kommer återvända till de lite mer avancerade exemplen, men först
   måste vi förklara vad som ligger bakom den här magin.

   Vad är ens en repl?


3. Read Eval Print Loop. Kommandorad. Terminal. Kopplad till det
   körande programmet.

   (def a [1 2 3])

   a

   (reduce + a)

   (reductions + a)

   Men vanligtvis skriver jag aldrig rakt i REPL:n utan arbetar med
   verktyg kopplade till den.

4. Med all den kontext som min källkod ger kan jag i det körande programmet.

   * Se vad värdet av symbolen.
   * Evaluera arbiträra uttryck.
   * Förändra programmet.

   Låt oss ta ett exempel. Säg att vi får en sorterad sekvens
   positioner för ett tåg från databasen och vill ge vidare en
   representation av tåget som dess nuvarande position samt ett trace
   av dess tidigare positioner.

   Här har vi en rad:

   (get-position pool {:turnummer "Simulator"}) ; =>

   {:id 1448, :turnummer "Simulator", :received #inst
   "2017-11-28T15:36:42.410627000-00:00", :latitude 37.33527476,
   :longitude -122.03254703, :speed 1.67, :heading 178.24}

   Och vad vi vill hämta ur denna är ju longituden och latituden.

   (:longitude (get-position pool {:turnummer "Simulator"})) ; => -122.03254703
   (:latitude  (get-position pool {:turnummer "Simulator"})) ; => 37.33527476

   Så vi skapar en funktion som tar dessa och stoppar i en vektor.

   (defn lat-lon-vec [position]
     [(:longitude position) (:latitude position)])

   För att sedan skapa ett helt trace tar vi och applicerar denna
   funktion på alla positioner i en sekvens.

   (map lat-lon-vec (get-positions pool {:turnummer "Simulator"}))

   Och gör det till en funktion.

   (defn make-trace [positions]
     (map lat-lon-vec positions))

   Och slutligen vill vi ju lägga in det här spåret i den slutliga
   representationen av tåget.

   (assoc (first (get-positions pool {:turnummer "Simulator"}))
       :trace
       (make-trace (get-positions pool {:turnummer "Simulator"})))

   Sedan gör vi även det till en funktion

   (defn positions->train-with-trace [positions]
     (assoc (first positions)
          :trace
          (make-trace positions)))



2. Och på samma sätt kan man arbeta med gränssnitt och funktioner.

   Gå till pages.cljs och ändra i en stil eller något:

   :font-size "20px"



5. Och även om man har en mer komplicerad kodbas, kanske med
   3D-grafik, fungerar det här.
