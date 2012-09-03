Abak-flow
=========
Нет, это не новая идеология ведения проекта, это всего лишь набор утилит которые помогают связать использование [git-flow](https://github.com/nvie/gitflow) и [github flow](http://scottchacon.com/2011/08/31/github-flow.html)

**Начиная с версии v0.2.1 используется авторизация OAuth2. [Как ей пользоваться](https://github.com/Strech/abak-flow/wiki/How-start-work-with-new-abak-flow)**

# Концепция
Идеология git-flow использует следующий набор веток:

* *master* - всегда пригодна для развертывания
* *develop* - основная ветка разработки
* *hotfix* - ветка для изменений которые попадут на продакшен сервер
* *feature* - ветки для крупных задач

Github-flow же наоборот ведет основную разработку в ветке master, но при этом master является пригодным для развертывания в любой момент.

После долгих раздумий было принято применить следующий набор правил, для разработки на github:

1. Вся разработка любой задачи и функционала ведется только в ветках **feature**
2. Разработаный функционал из ветки **feature** оформляется pull request только в ветку **develop**
3. Все исправления ошибок, которые должны попасть на продакшен сервер делаются только в ветках **hotfix**
4. Исправленные ошибки из ветки **hotfix** фофрмляются pull request только в ветку **master**
5. После получения исправлений на текущий момент в репозитории инициируется merge ветки **master** в **develop**


# Установка

    $ gem install abak-flow
    $ git config --global alias.request '!request'
    $ git config --global abak.apiuser YOUR_GITHUB_MAIL@gmail.com
    $ git config --global abak.apitoken 0123456789YOUR_GITHUB_API_TOKEN
    $ git remote add upstream git://github.com/GITHUB_PROJECT_USER/GITHUB_PROJECT_NAME.git
    
### А если я использую прокси, как быть?
    $ git config --global abak.proxy http://my-proxy.com:3129
    
Далее по приоритету идут переменные окружения. Сначала **http_proxy**, затем **HTTP_PROXY**

Т.е если вы используете переменные окружения, то просто не указывайте прокси в конфиге

---

**Заметьте:** В конфиге git, значением *abak.apiuser* должен являться тот email адрес, под которым вы заходите на github

**Обратите внимание:** В данном контексте под **upstream** подразумевается адрес репозитория в который будут оформляться pull request. А репозиторием **origin** будет являться ваш форк 

# С чего начать?

    $ git request readycheck

или
    
    $ git request help
    
**Примечание:** Вообще-то все комманды поддерживают опцию *--help*, но вот именно *git request --help* успевает перехватиться самим git и он конечно неодумевает как ему показать хэлп по внешней комманде

# Примеры использования
### Самый простой способ начать новую задачу:

    $ git checkout develop
    $ git request feature 'TASK-001'
    $ touch 'hello.txt' && echo 'Hello world!' > hello.txt
    $ git commit -a -m 'Hello world commit'
    $ git request publish

**Внимание:** Не нужно называться ветку TASK. Используйте префикс задачь из jira

Теперь то же самое, только словами:

* Переключимся в ветку develop
* Abak-flow создаст ветку, пригодную для оформления pull request (правила именования и правила самого реквеста)
* Простое создание нового файла
* Git процедуры добавления своих изменений в репозиторий
* Затем публикация вашей ветки на вашем форке (если таковая уже есть, то просто обновление), затем оформление pull request из этой ветки в соответствующую правилам ветку на upstream (в данном случае это будет ветка develop)

Для задач, которые должны быть выполнены в виде hotfix принцип тот же:

    $ git checkout master
    $ git request hotfix 'TASK-001'
    $ …
    $ git request publish

*На самом деле переключаться на master или develop в самом начале вовсе не обязательно, этот шаг был приведен для пущей ясности*

### Обновление ветки на удаленном репозитории:

    $ git checkout feature/TASK-001
    $ git request update

### Завершение текущей задачи:
Вообще, завершать задачу лучше только после того, как ваш pull request был принят. Почему? На самом деле по ряду причин. По умолчанию эта команда удаляет как вашу текущую ветку с задачей в локальном репозитории и в добавок ко всему - на вашем удаленном репозитории (форке)

    $ git checkout feature/TASK-001
    $ git request done 

Чтобы оставить какую либо ветку в живых возможно напрямую указать, какую копию ветки **удалить**, локальную или же удаленную (на origin)

    $ git checkout feature/TASK-001
    $ git request done --origin

Или же так

    $ git checkout feature/TASK-001
    $ git request done --local

## Маленькие хитрости
Если сразу правильно именовать ветки, т.е ветку с задачей создавать с именем, такого формата TASK-001, то, в описание pull request автоматически вставится ссылка на задачу в jira, а в имя pull request сразу вставится название, состоящее из имени задачи, т.е TASK-001

## А помощь?
Многие команды имеют какие-то дополнительные опции. Но они нужны только в экзотических случаях. Но при любом раскладе подсказку и тонкий намек всегда можно получить воспользовавших такой командой:

    $ git request done --help

# В заключении
Данный репозиторий и изложенные в нем идеи ни в коем случае не претендуют на идеал и совершенство. Это всего лишь узко заточенная комбинация гемов
