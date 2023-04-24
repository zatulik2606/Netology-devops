Домашнее задание к занятию «Инструменты Git»

Задание

В клонированном репозитории:

Найдите полный хеш и комментарий коммита, хеш которого начинается на aefea.



commit aefead2207ef7e2aa5dc81a34aedf0cad4c32545


Комментарий:Update CHANGELOG.md


Ответьте на вопросы.


1. Какому тегу соответствует коммит 85024d3?



commit 85024d3100126de36331c6982bfaac02cdab9e76


Тег: v0.12.23



2. Сколько родителей у коммита b8d720? Напишите их хеши.



parent 56cd7859e05c36c06b56d013b55a252d0bb7e158


parent 9ea88f22fc6269854151c571162c5bcf958bee2


3. Перечислите хеши и комментарии всех коммитов, которые были сделаны между тегами v0.12.23 и v0.12.24.



git log v0.12.23..v0.12.24


commit 33ff1c03bb960b332be3af2e333462dde88b279e (tag: v0.12.24)


v0.12.24


commit b14b74c4939dcab573326f4e3ee2a62e23e12f89


[Website] vmc provider links



commit 3f235065b9347a758efadc92295b540ee0a5e26e

Update CHANGELOG.md



commit 6ae64e247b332925b872447e9ce869657281c2bf


registry: Fix panic when server is unreachable


Non-HTTP errors previously resulted in a panic due to dereferencing the resp pointer while it was nil, as part of rendering the error message. This * commit changes the error message formatting to cope with a nil response, and extends test coverage.


Fixes #24384



commit 5c619ca1baf2e21a155fcdb4c264cc9e24a2a353


*website: Remove links to the getting started guide's old location


Since these links were in the soon-to-be-deprecated 0.11 language section, I think we can just remove them without needing to find an equivalent link.




commit 06275647e2b53d97d4f0a19a0fec11f6d69820b5


Update CHANGELOG.md




commit d5f9411f5108260320064349b757f55c09bc4b80


command: Fix bug when using terraform login on Windows




commit 4b6d06cc5dcb78af637bbb19c198faff37a066ed


Update CHANGELOG.md




commit dd01a35078f040ca984cdd349f18d0b67e486c35


Update CHANGELOG.md




commit 225466bc3e5f35baa5d07197bbc079345b77525e


Cleanup after v0.12.23 release



4. Найдите коммит, в котором была создана функция func providerSource, её определение в коде выглядит так: func providerSource(...) (вместо троеточия перечислены аргументы).




commit 5af1e6234ab6da412fb8637393c5a17a1b293663 Author: Martin Atkins mart@degeneration.co.uk Date: Tue Apr 21 16:28:59 2020 -0700




commit 8c928e83589d90a031f811fae52a81be7153e82f Author: Martin Atkins mart@degeneration.co.uk Date: Thu Apr 2 18:04:39 2020 -0700



Определяем по раннее созданному:




git show 8c928e8 | grep "func providerSource" +func providerSource(services *disco.Disco) getproviders.Source {



Значит это commit: 8c928e83589d90a031f811fae52a81be7153e82f


5. Найдите все коммиты, в которых была изменена функция globalPluginDirs.



git log -S 'globalPluginDirs'



commit 65c4ba736375607b6af6c035972f7f151232b6c6 Author: Valeriy Pastushenko i@combin.name Date: Sat May 21 19:53:24 2022 +0300


Remove terraform binary



commit 125eb51dc40b049b38bf2ed11c32c6f594c8ef96 Author: Alisdair McDiarmid alisdair@users.noreply.github.com Date: Thu May 5 10:12:00 2022 -0400


Remove accidentally-committed binary


Also add this path to .gitignore to prevent future mistakes.



commit 22c121df8631c4499d070329c9aa7f5b291494e1 Author: Anna Winkler 3526523+annawinkler@users.noreply.github.com Date: Tue May 3 12:28:41 2022 -0600


Bump compatibility version to 1.3.0 for terraform core release (#30988)


* Bump compatibility version to 1.3.0 for terraform core release



Co-authored-by: Brandon Croft <brandon.croft@gmail.com>



commit 7c7e5d8f0a6a50812e6e4db3016ebfd36fa5eaef Author: Valeriy Pastushenko i@combin.name Date: Fri Sep 3 18:33:10 2021 +0300

Don't show data while input if sensitive



commit 35a058fb3ddfae9cfee0b3893822c9a95b920f4c Author: Martin Atkins mart@degeneration.co.uk Date: Thu Oct 19 17:40:20 2017 -0700



main: configure credentials from the CLI config file



commit c0b17610965450a89598da491ce9b6b5cbd6393f Author: James Bardin j.bardin@gmail.com



6. Кто автор функции synchronizedWriters?




commit 5ac311e2a91e381e2f52234668b49ba670aa0fe5


Author: Martin Atkins mart@degeneration.co.uk Date: Wed May 3 16:25:41 2017 -0700



Ответ: Martin Atkins


В качестве решения ответьте на вопросы и опишите, как были получены эти ответы. Правила приёма домашнего задания
В личном кабинете отправлена ссылка на .md-файл в вашем репозитории.

