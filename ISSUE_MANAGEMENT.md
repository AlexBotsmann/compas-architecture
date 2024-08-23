<!--
SPDX-FileCopyrightText: 2021 Alliander N.V.

SPDX-License-Identifier: CC-BY-4.0
-->

## Керування проблемами
Для керування проблемами ми використовуємо трекер проблем у GitHub. Проблеми створюються в репозиторії, де є пов’язані з ними питання.
Питання, які стосуються всього проекту CoMPAS, додаються як питання в репозиторії "compas-architecture". Пізніше їх, можливо, буде переміщено до правильного репозиторію. 

Щоб отримати огляд усіх проблем, ми використовуємо панель проекту на GitHub.
На рівні організації існує дві оглядові панелі для моніторингу всіх проблем і запитів у різних репозиторіях.
Одна для проблем, CoMPAS Issues Overview Board, а інша для pull requests, CoMPAS Pull Request Overview Board.
Кожен репозиторій, який може містити проблеми та запити, пов'язаний з цими двома наглядовими радами проекту.

Для управління цією екосистемою питань у різних сховищах додається певна автоматизація, щоб полегшити життя.

### Налаштування (нового) репозиторію
Коли створюється новий репозиторій (або потрібно налаштувати існуючий), необхідно виконати деякі налаштування, щоб автоматизація працювала.

Перейдіть до нового репозиторію і виберіть вкладку «Settings». У налаштуваннях виберіть пункт «Options». 
У розділі «Функції» переконайтеся, що обрано опції «Issues» та «Projects». 
Тепер зліва від «Settings» мають з'явитися дві нові вкладки «Issues» та «Projects».

Далі перейдіть на вкладку «Projects» і створіть нову панель проекту (права верхня кнопка).
Дайте панелі проекту нове ім'я, наприклад, «CoMPAS \<Project\> Board», за бажанням дайте їй опис, використовуйте шаблон «Basic kanban».
Тепер створіть панель проекту. Ця панель проекту буде використовуватися для огляду на рівні репозиторію з усіма проблемами і запитами.
Далі буде показано панель проекту. Видаліть нотатки, які будуть створені автоматично.

Тепер для кожного стовпчика виберіть «КManage automation» під «...». Змініть автоматизацію відповідно до таблиці нижче і оновіть налаштування.

| Зробити                        | В прогресі                                 | Зроблено                                               |
| ---------------------------- |:-------------------------------------------:| --------------------------------------------------:|
| Issue: Новододана, Знов відкрита |Issue: _None_                                | Issue: Закрита                                      |
| Pull Request: Новододана    |Pull Request: Знов відкрита, схвалена рецензентом| Pull Request: об'єднано, закрито з не об'єднаними комітами |
|                              | Очікує на схвалення рецензента                |                                                    |

Далі перейдіть до огляду організації CoMPAS і перейдіть на вкладку «Projects».

Тепер нам потрібно зв’язати нове сховище з обома панелями проекту. Повторіть наведену нижче частину для обох дощок проекту «Панель огляду питань CoMPAS» і «Панель огляду запитів на вилучення CoMPAS».
Не відкривайте панель, але під «...» (в кінці рядка) виберіть «Settings». Далі перейдіть до «Linked repositories» і прив'яжіть нове сховище за допомогою кнопки «Link a repository».
Репозиторій з'явиться у списку прив'язаних репозиторіїв, тепер можна також додавати питання і запити на форумі.

### Додати дію GitHub 
Для автоматичного додавання питань та pull request на панель проєкту ми додамо відповідну дію на GitHub.

У репозиторії створіть файл «automate_projects.yml» в директорії «.github/workflows», що містить наступний вихідний код: 

```yaml
name: Add issues and pull request to project boards

on: [ issues, pull_request ]

jobs:
  github-actions-automate-projects:
    runs-on: ubuntu-latest
    steps:
      - name: add-new-issues-to-repository-based-project-column #(Step 1)
        uses: docker://takanabe/github-actions-automate-projects:v0.0.1
        if: github.event_name == 'issues' && github.event.action == 'opened'
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          GITHUB_PROJECT_URL: https://github.com/com-pas/compas-architecture/projects/2 #(1)
          GITHUB_PROJECT_COLUMN_NAME: To do
      - name: add-new-pull-request-to-repository-based-project-column #(Step 2)
        uses: docker://takanabe/github-actions-automate-projects:v0.0.1
        if: github.event_name == 'pull_request' && github.event.action == 'opened'
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          GITHUB_PROJECT_URL: https://github.com/com-pas/compas-architecture/projects/2 #(1)
          GITHUB_PROJECT_COLUMN_NAME: To do
      - name: add-new-issues-to-organization-based-project-column #(Step 3)
        uses: docker://takanabe/github-actions-automate-projects:v0.0.1
        if: github.event_name == 'issues' && github.event.action == 'opened'
        env:
          GITHUB_TOKEN: ${{ secrets.ORG_GITHUB_ACTION_SECRET }}
          GITHUB_PROJECT_URL: https://github.com/orgs/com-pas/projects/1 #(2)
          GITHUB_PROJECT_COLUMN_NAME: To do
      - name: add-new-pull-request-to-organization-based-project-column #(Step 4)
        uses: docker://takanabe/github-actions-automate-projects:v0.0.1
        if: github.event_name == 'pull_request' && github.event.action == 'opened'
        env:
          GITHUB_TOKEN: ${{ secrets.ORG_GITHUB_ACTION_SECRET }}
          GITHUB_PROJECT_URL: https://github.com/orgs/com-pas/projects/2 #(3)
          GITHUB_PROJECT_COLUMN_NAME: To do
```
Дія на GitHub складається з 4 кроків. 
- (Крок 1): Додайте нові випуски на панель проектів сховища.
- (Крок 2): Додайте нові запити на панель проектів сховища.
- (Крок 3): Додайте нові випуски на панель проектів організації «CoMPAS Issues Overview Board».
- (Крок 4): Додайте нові pull-запити на панель проектів організації «CoMPAS Pull Request Overview Board».

Кілька пунктів:
- (1): URL до панелі проектів сховища.
- (2): URL до проектної панелі організації «CoMPAS Issues Overview Board».
- (3): URL до проектної панелді організації «CoMPAS Pull Request Overview Board».

### Configuration Organisation Project Board
Як вже згадувалося, на рівні організації існує дві проектні панелі, а саме «CoMPAS Issues Overview Board» та «CoMPAS Pull Request Overview Board». Для більшої автоматизації ми змінили деякі налаштування на обох панелях.

Відкрийте обидві панелі і змініть налаштування автоматизації стовпців (у стовпці виберіть «Manage automation» під кнопкою «...») відповідно до таблиць нижче.

Проєктна панель «CoMPAS Issues Overview Board»:

| Зробити                        | В процесі                                 | Зроблено                                               |
| ---------------------------- |:-------------------------------------------:| --------------------------------------------------:|
| Issue: Новододана, Знов відкрита |Issue: _None_                                | Issue: Закрита                                      |
| Pull Request: _None_         |Pull Request: _None_                         | Pull Request: _None_                               |


Проєктна панель "CoMPAS Pull Request Overview Board":

| Зробитм                        | В процесі                                 | Зроблено                                               |
| ---------------------------- |:-------------------------------------------:| --------------------------------------------------:|
| Issue: _None_                |Issue: _None_                                | Issue: _None_                                      |
| Pull Request: Новододана    |Pull Request: Знов відкрита, схвалена рецензентом | Pull Request: об'єднано, закрито з не об'єднаними комітами |
|                              | Очікує на схвалення рецензента                |                                                    |

### Додавання секрету дії ORG_GITHUB_ACTION_SECRET
Для доступу до проектних панелей організації необхідно створити секретний токен ORG_GITHUB_ACTION_SECRET.
- Спочатку створіть новий персональний токен доступу з https://github.com/settings/tokens. Токени можуть бути створені тільки як особисті токени.
  Токен також повинен мати права «admin:org». Це опосередковано також встановить права «write:org» і «read:org». 
- Далі створіть новий секрет організації з https://github.com/organizations/com-pas/settings/secrets/actions зі значенням 
  персонального токену доступу, який ви створили вище. Назвіть секрет ORG_GITHUB_ACTION_SECRET. Зробіть його доступним для публічних сховищ.

Тепер акція може використовувати цей секрет для додавання питань і pull request на панелі проєктів організації.
