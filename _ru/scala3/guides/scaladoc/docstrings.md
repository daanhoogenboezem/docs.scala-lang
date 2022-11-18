---
layout: multipage-overview
title: Docstrings - специфичные теги и особенности
partof: scala3-scaladoc
language: ru
num: 2
previous-page: index
next-page: linking
---

В этой главе описывается, как правильно писать строки документации и как использовать все доступные функции scaladoc.
Так как многое осталось таким же, как и в старом scaladoc, некоторые детали взяты из этой 
[статьи](https://docs.scala-lang.org/overviews/scaladoc/for-library-authors.html).

Scaladoc расширяет возможности Markdown дополнительными функциями, такими как ссылки на определения API. 
Это можно использовать в статической документации и постах в блогах для создания смешанного контента.

## Куда поместить строки документации

Комментарии Scaladoc идут перед элементами, к которым они относятся, в специальном блоке комментариев, 
который начинается с `/**` и заканчивается `*/`, например:

```scala
/** Комментарий начинается здесь.
  * Левая "звезда", за которой следует пробел в каждой строке,
  * позволяет продолжить комментарий.
  *
  * Даже на пустых строках разрыва абзаца.
  *
  * Обратите внимание, что '*' в каждой строке выровнена
  * со вторым '*' в '/**' так, чтобы
  * левое поле находилось в том же столбце, где
  * первая строка и последующие.
  *
  * Комментарий закрывается с помощью '*' и обратного слэша.
  *
  * Если используются теги Scaladoc (@param, @group и т.д.),
  * не забудьте поместить их в отдельные строки без предшествующих им строк.
  *
  * Например:
  *
  * Рассчитать квадрат заданного числа
  *
  * @param d the Double to square
  * @return the result of squaring d
  */
 def square(d: Double): Double = d * d
```

В приведенном выше примере этот комментарий Scaladoc связан с методом square, 
поскольку он находится прямо перед ним в исходном коде.

Комментарии Scaladoc могут идти перед полями, методами, классами, трейтами, объектами. 
На данный момент scaladoc не поддерживает прямое решение для документирования пакетов. 
На гитхабе есть специальный [issue](https://github.com/lampepfl/dotty/issues/11284), где вы можете проверить текущий статус проблемы.

Для первичных конструкторов класса, которые в Scala совпадают с определением самого класса, 
тег @constructor используется для указания комментария, помещаемого в документацию первичных конструкторов, а не в обзор класса.

## Теги

Scaladoc использует теги `@<tagname>`для предоставления определенных подробностей полей в комментариях. 
Теги включают:

### Теги, специфичные для класса

- `@constructor` помещенный в комментарий класса, будет описывать первичный конструктор.

### Теги, специфичные для метода

- `@return` для детализации возвращаемого значения из метода (по одному на метод).

### Теги метода, конструктора и/или класса

- `@throws` какие исключения (если есть) может генерировать метод или конструктор.
- `@param` детализация параметра метода или конструктора, предоставляется по одному `@param` для каждого параметра метода/конструктора.
- `@tparam` детализация параметра типа для метода, конструктора или класса. Указывается по одному для каждого параметра типа.

### Теги использования

- `@see` ссылки на другие источники информации, такие как ссылки на внешние документы или связанные объекты в документации.
- `@note` добавление примечания о предварительных или последующих условиях или любых других заметных ограничениях или ожиданиях.
- `@example` предоставление примера кода или соответствующей документации.


### Теги группировки

Эти теги хорошо подходят для больших типов или пакетов со многими элементами. 
Они позволяют организовать страницу Scaladoc в отдельные разделы, каждый из которых отображается отдельно в выбранном порядке.

Эти теги не включены по умолчанию! Необходимо передать флаг `-groups` в Scaladoc, чтобы включить их. 
В sbt это обычно выглядит примерно так:

```scala
Compile / doc / scalacOptions ++= Seq(
  "-groups"
)
```

Каждый раздел должен иметь идентификатор из одного слова, который используется во всех этих тегах, как показано ниже в `group`. 
По умолчанию этот идентификатор отображается как заголовок раздела документации, 
но можно использовать `@groupname`, чтобы указать более длинный заголовок.

Как правило, необходимо поместить `@groupprio` (и, возможно, `@groupname` и `@groupdesc`) в Scaladoc для самого пакета/трейта/класса/объекта, 
описывая все группы и их порядок. Затем поместить `@group` в Scaladoc для каждого члена, указав, в какой группе он находится.

Члены, у которых нет тега `@group`, будут перечислены в результирующей документации как “Ungrouped”.

- `@group <group>` - пометить сущность как члена `<group>` группы
- `@groupname <group> <name>` - указание необязательного имени для группы. `<name>` отображается как заголовок группы перед её описанием.
- `@groupdesc <group> <description>` - добавление необязательного описания для отображения под именем группы. Поддерживает многострочный форматированный текст.
- `@groupprio <group> <priority>` - управление порядком группы на странице. По умолчанию 0. Несгруппированные элементы имеют неявный приоритет 1000. 
  Используются значения от 0 до 999, чтобы задать положение относительно других групп. Малые значения появятся перед большими значениями.

### Другие теги

- `@author` предоставление информации об авторе для следующего объекта.
- `@version` версия системы или API, частью которой является этот объект.
- `@since` похож на `@version`, но определяет систему или API, в котором эта сущность была впервые определена.
- `@deprecated` помечает объект как устаревший, предоставляя как замену реализации, которую следует использовать, 
  так и версию/дату, когда этот объект устарел.
- `@syntax <name>` позволяет изменить парсер для docstring. Синтаксис по умолчанию — markdown, 
  однако можно изменить его с помощью этой директивы. В настоящее время доступны синтаксисы `markdown` или `wiki`. 
  Пример использования: `@syntax wiki`.

### Макросы

- `@define <name> <definition>`  позволяет использовать $name в других комментариях Scaladoc в том же исходном файле, который будет заменен на `<definition>`.

Если комментарий не предоставляется для объекта на текущем уровне наследования, 
но предоставляется для переопределенного объекта на более высоком уровне иерархии наследования, 
будет использоваться комментарий из суперкласса.

Аналогично, если `@param`, `@tparam`, `@return` и другие теги сущностей опущены, но доступны из суперкласса, будут использоваться из суперкласса.

### Явный

Для явного наследования комментариев используется тег `@inheritdoc`.

### Разметка

Scaladoc предоставляет два анализатора синтаксиса: `markdown` (по умолчанию) или `wikidoc`. 
В Scaladoc по-прежнему можно встраивать теги HTML (как и в Javadoc), но в большинстве случаев это не обязательно, 
поскольку вместо этого может использоваться разметка.

#### Markdown

Markdown использует [вариант commonmark](https://spec.commonmark.org/current/) с двумя пользовательскими расширениями:
- `wikidoc` ссылки для удобства
- `wikidoc`кодовые блоки с синтаксисом фигурных скобок

#### Wikidoc

Wikidoc — это синтаксис, используемый для scala2 scaladoc.
Он поддерживается из-за многих существующих исходников, однако **не** рекомендуется его использование в новых проектах. 
Синтаксис вики можно включить с помощью глобального флага `-comment-syntax wiki` или с помощью `@syntax wiki` директивы в строке документации.

Некоторые из стандартных доступных разметок:

```
`monospace`
''italic text''
'''bold text'''
__underline__
^superscript^
,,subscript,,
[[entity link]], e.g. [[scala.collection.Seq]]
[[https://external.link External Link]], e.g. [[https://scala-lang.org Scala Language Site]]
```

Для получения дополнительной информации о вики-ссылках см. [эту главу](#связывание-с-api).

Другие примечания по форматированию

- Абзацы начинаются с одной (или нескольких) пустых строк. `*` на полях для комментария допустимо (и должно быть включено), 
  в противном случае строка должна оставаться пустой.
- Заголовки определяются окружающими символами `=` с большим количеством `=` для обозначения подзаголовков. 
  Например `=Heading=`, `==Sub-Heading==` и т.д.
- Блоки списка представляют собой последовательность элементов списка с одинаковым стилем и уровнем, 
  без прерываний от других стилей блоков. Неупорядоченные списки можно маркировать с помощью `-`, 
  нумерованные списки можно обозначать с помощью `1.`, `i.`, `I.` или `a.` для различных стилей нумерации. 
  В обоих случаях должно быть дополнительное пространство впереди, а большее пространство создает подуровень.

Разметка для блоков списка выглядит так:

```
/** Вот неупорядоченный список:
  *
  *   - Первый элемент
  *   - Второй элемент
  *     - Подпункт ко второму
  *     - Еще один подпункт
  *   - Третий пункт
  *
  * Вот упорядоченный список:
  *
  *   1. Первый пронумерованный элемент
  *   1. Второй номер позиции
  *     i. Подпункт ко второму
  *     i. Еще один подпункт
  *   1. Третий пункт
  */
```

### Общие примечания по написанию комментариев к Scaladoc

Краткость - это хорошо! Быстро переходите к сути, у людей ограничено время, которое они могут потратить на вашу документацию, 
используйте его с умом. Опустите ненужные слова. "Prefer возвращает X", а не "этот метод возвращает X", 
и "X, Y и Z", а не "этот метод возвращает X, Y и Z". 
Принцип DRY (_Don’t repeat yourself_) - не повторяйтесь. 
Не дублируйте описание метода в теге `@return` и других формах повторяющихся комментариев.

### Подробнее о написании Scaladoc

Дополнительную информацию о рекомендациях по форматированию и стилю можно найти 
в [руководстве по стилю Scala-lang scaladoc](https://docs.scala-lang.org/style/scaladoc.html).

## Связывание с API

Scaladoc позволяет ссылаться на документацию по API с помощью ссылок в стиле Wiki. 
Связать с `scala.collection.immutable.List` так же просто, как указать `[[scala.collection.immutable.List]]`. 
Для получения дополнительной информации о точном синтаксисе см. [ссылки в документации](/ru/scala3/guides/scaladoc/linking.html#определение-ссылок).