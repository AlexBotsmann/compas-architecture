<!--
SPDX-FileCopyrightText: 2021 Alliander N.V.

SPDX-License-Identifier: CC-BY-4.0
-->

## XSD Розширення
Якщо з якихось причин вам потрібні розширені елементи/атрибути поряд зі схемами IEC XSD, ми визначаємо окремі схеми XSD.

Вони зберігаються в `scl-extension` модулі [CoMPAS Core repository](https://github.com/com-pas/compas-core).

Прикладом може слугувати [CoMPAS XSD](https://github.com/com-pas/compas-core/blob/develop/scl-extension/src/main/resources/xsd/SCL_CoMPAS.xsd).

Іншим варіантом було використання [CoMPAS IEC XSD repository](https://github.com/com-pas/compas-scl-xsd), але ми хочемо зберегти цей репозиторій, присвячений схемам IEC XSD.

#### CoMPAS-широкі розширення XSD
Якщо нам потрібні специфічні для CoMPAS XSD-схеми, вони можуть бути додані до `scl-extension` модуля [CoMPAS Core repository](https://github.com/com-pas/compas-core) як зазначено вище. Достатньо лише додати їх, проект, що включає залежність, повинен переконатися, що вона використовується правильно, наприклад, перевірка повинна виконуватися самим додатком.

### Приклад
Припустимо, ви хочете використати елемент `Filename` та елемент `FileType`, який було оголошено у файлі `SCL_CoMPAS.xsd`, що знаходиться у модулі `cl-extension` у [CoMPAS Core Repository](https://github.com/com-pas/compas-core). Ми робимо це шляхом додавання:

```xml
<!-- #(1) -->
<SCL xmlns="http://www.iec.ch/61850/2003/SCL" xmlns:compas="https://www.lfenergy.org/compas/v1">
	<Header id="someId"/>
	<!-- #(2) -->
 	<Private type="compas_scl">
		<!-- #(3) -->
	 	<compas:Filename>MyFilename</compas:Filename>
	 	<compas:FileType>SSD</compas:FileType>
	</Private>
	<Substation name="mySubstation"/>
</SCL>
```

Кілька пунктів:
- (1): Включіть простір імен включених XSD-елементів та/або атрибутів, у цьому випадку це `https://www.lfenergy.org/compas/v1`. Дайте йому префіксне ім'я, наприклад (у цьому випадку) `compas`.
- (2): Створіть приватний елемент у SCL. 
  Ми могли б оголошувати елементи, не оточуючи їх Private, але перевага використання приватних елементів полягає в тому, що вміст даних зберігається при обміні даними між інструментами. Для отримання додаткової інформації див. розділ 8.3.6 стандарту IEC-61850-6. Крім того, тип є важливим аспектом приватного елемента (також тому, що він є обов'язковим). Для приватних елементів CoMPAS ми використали тип `"compas_scl"`, щоб відрізнити його від інших приватних елементів.
- (3): Оголосіть елементи всередині елемента Private, у цьому випадку елементи `Filename` та `FileType`.

**Переконайтеся, що валідація виконана правильно за допомогою власної валідації проекту!**
