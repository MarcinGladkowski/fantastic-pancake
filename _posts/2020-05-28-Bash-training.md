---
layout: single
title: Bash training
---

I oto kolejny wpis. Tym razem o języku powłoki Bash. Nie będę rozpisywał się o jego historii czy specyfice. Wszystko można znaleźć bez problemu w internecie. 

Dlaczego zacząłem go trenować? Szybkie napisanie skyptu automatyzującego jakąś czynność jest bezcenna. Oczywiście do automatyzacji można polecić inne języki takie jak Python czy Go. Jednak taki skrypt powłoki zadziała prawie na każdym środowisku linuxowym (piszę prawie bo jestem przekonany, że mogą istnieć jakieś wyjątki). Mimo 2020 roku takie skrypty wykorzystywane są np. przez obrazy dockera gdzie to możemy wykorzystać do budowy naszego środowiska skrypty takie jak ```docker-entrypoint.sh``` ```wait-for-it.sh```. Jak każda składania posiada swoje specyficzne komendy, czy triki. Jednak nie trzeba wiedzieć wszystkiego.

Ten tydzień/lub dłuższy okres czasu spędzam wolny czas na ćwiczeniu pisania dobrych skryptów Bash. 

Znalazłem niezły przewodnik w j.polskim, oraz zaczęrpnąłem trochę wiedzy z bloga Devopsiarz, oraz utworzyłem swoje repozytorium na napisany kod.
* [przewodnik](https://bash.0x1fff.com/)
* [devopsiarz.pl](https://devopsiarz.pl/bash/tutorial-kurs-pisania-dobrych-skryptow-bash-wstep-dobre-praktyki-shellcheck/)
* [trening_bash](https://github.com/MarcinGladkowski/bash-training)

Jeden z moich skryptów napisany po tym krótkim treningu to skrypt w tym repozytorium do tworzenia szablonu nowego wpisu ```./scripts/newPost.sh```.

W następnych wpisach chciałbym zebrać najlepsze praktyki i pomysły na realizację pomocnych skryptów powłoki Bash.

_Pozdrawiam_
_Marcin_