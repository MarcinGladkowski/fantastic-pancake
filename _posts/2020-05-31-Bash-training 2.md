---
layout: single
title: Bash training 2
---

### Słowem wstępu

W tym wpisie skupię się na możliwościach skryptów powłoki Bash, bezpieczeństwie i dobrych praktykach tworzenia skryptów.

Oczywiście temat nie będzie prze zemnie wyczerpany, a jest jedynie podstawą dla początkujących tak aby tworzyć wystarczająco dobre skrypty oraz zapobiec pewnym powszechnym błędom. 

W moim repozytoium [bash_training](https://github.com/MarcinGladkowski/bash-training) są podstawowe skrypty gdzie zapoznawałem się składnią języka.

### Bezpieczeństwo

#### Przytoczę najważniejsze zasady działania z różnymi skryptami, w momencie gdy mamy coś zrobić przy ich pomocy: 
* Musisz być ostrożnym co wykonujesz!
* Nie uruchamiaj wszystkiego z uprawnieniami **root**
* Dodaj _alias_ na komendy systemowe, ktore mogą narobić najwięcej szkód! (np: alias='rm -i')
* Jest wiele możliwości ataku! - nie uruchamiaj skryptów o dziwnej dla Ciebie zawartości
  
### Jak napisać wystarczająco dobry skrypt powłoki ?

Należy pamiętać, że dobrze napisany skrypt urchoni nas przed problemami. Gdy zrobimy to niedbale możemy stracić ważne dla nas pliki czy uszkodzić nasz system. Mniejszy problem, gdy będzie to tylko nasze lokalne środowisko.

Rady opisane poniżej będą zaczerpnięte z wpisu na blogu [https://devopsiarz.pl - wpis](https://devopsiarz.pl/bash/tutorial-kurs-pisania-dobrych-skryptow-bash-wstep-dobre-praktyki-shellcheck/).

Po napisaniu własnego skryptu warto użyć narzędzia do sprawdzenia poprawności składni i potencjalnych problemów. Polecane jest narzędzie **shellcheck**. Do instalacji na _Ubuntu_ wystarczy użyć standardowego managera paczek ```sudo apt-get install  -y shellcheck```

Użyjemy tego narzędzia do analizy skryptu, którego napisałem ostatnii na potrzeby dodawania nowego pliku z wpisem dla Jeckyll. 
```bash
#!/bin/bash

POSTS_DIR="${PWD}/_posts/"
echo "Create new post"
date=$(date '+%Y-%m-%d')
echo "Default time for post is $date"

echo "Set title of post"
read title # find idea to get title (set maximum arguments)
# parse setting -
postFile="$date-${title/ /-}.md"

if [ -f $postFile ]; then
   echo "Post with name $postFile exists!" 
   # want to delete ?
fi

NEW_POST_FILE="${POSTS_DIR}${postFile}"

touch "${POSTS_DIR}${postFile}"
echo "Created new post! ${postFile}"

tee -a ${NEW_POST_FILE} <<HEADERS
---
layout: default
title: ${title}
---
HEADERS
```

Wykonajmy teraz analizę przy pomocy ```shellcheck``` wykonując komendę: ```shellcheck ./scripts/newPost.sh```

Output w wierszu poleceń:
```bash
read title # find idea to get title (set maximum arguments)
^--^ SC2162: read without -r will mangle backslashes.


In ./scripts/newPost.sh line 13:
if [ -f $postFile ]; then
        ^-------^ SC2086: Double quote to prevent globbing and word splitting.

Did you mean: 
if [ -f "$postFile" ]; then


In ./scripts/newPost.sh line 23:
tee -a ${NEW_POST_FILE} <<HEADERS
       ^--------------^ SC2086: Double quote to prevent globbing and word splitting.

Did you mean: 
tee -a "${NEW_POST_FILE}" <<HEADERS

For more information:
  https://www.shellcheck.net/wiki/SC2086 -- Double quote to prevent globbing ...
  https://www.shellcheck.net/wiki/SC2162 -- read without -r will mangle backs...
```
Z powyższej analizy wystarczy, że jeśli dodamy cudzysłów w dwóch miejscach i parametr ```-r``` do odczytywania inputu możemy poprawić działanie skryptu. Oczywiście to jednak trochę za mało.

Z przyzwyczajenia często używałem zapisu zmiennych z postaci `postFile`, ale z tego co widzę lepiej z wygląda zapis w postaci `POST_FILE` lub `post_file`.

Z przytoczonego prze ze mnie wpisu można jednak wyciągnąć dużo więcej.

Jako, że nigdy nie pisałem za dużo skryptów `Bash` można się jeszcze dużo nauczyć.

Czego można by jeszcze opisać i nauczyć się, a jest jeszcze sporo tego. Mimo, że jest XXI skrypty `Bash` można spotkać wszędzie np. gdy tworzysz np swój obraz dokera w `Dockerfile`. 

Co chciałym jeszcze opisać:
* Jak tworzyć odpowiednie warunki.
* Sprawdznie odpowiednich oprawnień.
* Tworzenie odpowiedniego skryptu z parametrami przy użyciu `getopt`.
* Kody błędów oraz zwracanie ich, obsługa błędów.

Pozdrawiam 
Marcin