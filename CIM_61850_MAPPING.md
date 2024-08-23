<!--
SPDX-FileCopyrightText: 2021 Alliander N.V.

SPDX-License-Identifier: CC-BY-4.0
-->

## Mapping CIM - IEC 61850

Ми отримали приклад зіставлення від RTE, вони вже створили зіставлення між файлами CIM і файлами IEC 61850.
Ми використовували стандарт IEC 623610-102, щоб порівняти його з прикладом, який ми отримали від RTE.

Мета полягає в тому, щоб порівняти кілька компонентів, щоб побачити, чи ми можемо сказати: ми можемо використовувати це відображення!
Таким чином, нам не доведеться з’ясовувати все самим.

### Findings
У прикладі зіставлення ми бачимо такі компоненти CIM:

Bay (Відсік)
Breaker (Вимикач)
ConnectivityNode (Вузол приєднання)
Disconnector (Роз'єднувач)
EnergyConsumer (Споживач енергії)
LoadBreakSwitch (Вимикач навантаження)
PowerTransformer (Силовий Трансформатор)
PowerTransformerEnd (Вивід силового трансформатора)
Substation (Пвдстанція)
Terminal (Термінал)
VoltageLevel (Рівень напруги)

- З підстанцією ніби все гаразд, можуть бути складні ситуації, про які я не знаю.
- *PowerTransformer* виглядає незавершеним. Відповідно до стандарту IEC 62361-102 еквівалентом SCL є «TransformerWinding», це робиться інакше.
- Такі компоненти, як Terminal і Bay, виглядають добре!

#### Нестача деяких SCL елементів
- whole 'Process' SCL element mapping
- whole 'Line' SCL element mapping
- whole 'GeneralEquipment' SCL element mapping
- whole 'SubEquipment' SCL element mapping
- whole 'Function'/'SubFunction'/'EqFunction'/'EqSubFunction' SCL element mapping
- whole 'Tapchanger' SCL element mapping

## Питання
- файл open_substation.scd містить секцію SCL. Чи вважається це заголовком? (Тому що у ньому містяться такі дані, як версія та ревізія)
- Що робити з розділом заголовка SCL-файлу? Створити новий?

## Висновок
Частина схем відповідає стандарту, частина схем не містить стандартної інформації (наприклад, «Process» і «Line»).
Я вважаю, що його можна використовувати як довідник, але ми повинні самі заглибитися в IEC 62361-102, щоб завершити мапування.

## Джерело

- IEC/TS 62361-102:2018