# Поддержка Electron

## Поиск поддержки

Если у вас есть проблема с безопасностью, смотрите [документ безопасности](https://github.com/electron/electron/tree/master/SECURITY.md).

Если вы ищете помощь в программировании, для ответов на вопросы, или присоединиться к обсуждению с другими разработчиками, которые используют Electron, вы можете взаимодействовать с сообществом в этих местах:
- [`electron`](https://discuss.atom.io/c/electron) категория на Atom форумы
- `#atom-shell` канал на Freenode
- `#electron` канал на [Atom Slack](https://discuss.atom.io/t/join-us-on-slack/16638?source_topic_id=25406)
- [`electron-ru`](https://telegram.me/electron_ru) *(Русский)*
- [`electron-br`](https://electron-br.slack.com) *(Бразильский Португальский)*
- [`electron-kr`](https://electron-kr.github.io/electron-kr) *(Корейский)*
- [`electron-jp`](https://electron-jp.slack.com) *(Японский)*
- [`electron-tr`](https://electron-tr.herokuapp.com) *(Турецкий)*
- [`electron-id`](https://electron-id.slack.com) *(Индонезия)*
- [`electron-pl`](https://electronpl.github.io) *(Польша)*

Если вы хотите внести свой вклад в Electron, смотрите [документ](https://github.com/electron/electron/blob/master/CONTRIBUTING.md).

Если вы обнаружили ошибку в [поддерживаемой версии](#supported-versions) Electron, сообщите об этом [issue tracker](../development/issues.md).

[awesome-electron](https://github.com/sindresorhus/awesome-electron) - поддерживаемый сообществом список полезных примеров приложений, инструментов и ресурсов.

## Поддерживаемые версии

Последние три *стабильных* версии поддерживаются командой Electron. Например, если последний релиз 6.1.x, то поддерживается и 5.0.x, а также серия 4.2.x.  Мы поддерживаем только последний минорный релиз для каждой стабильной серии.  Это означает, что в случае исправления безопасности 6.1. , но мы не будем выпускать новую версию 6.0.x.

Последняя стабильная версия в одностороннем порядке получает все исправления от `master`, и версия до этого получает подавляющее большинство этих исправлений в качестве гарантов на время и пропускную способность. Самая старая поддерживаемая строка релиза будет получать только исправления безопасности.

Все поддерживаемые релизы будут принимать внешние Pull Request'ы к backport исправления ранее объединенные к `master`, хотя это может быть в каждом конкретном случае, для некоторых старых поддерживаемых строк. Все спорные решения относительно выпуска строк будут решены Рабочей группой [Релизы](https://github.com/electron/governance/tree/master/wg-releases) в качестве пункта повестки дня еженедельного совещания на неделе, за которую будет поднят backport PR.

Когда API изменен или удален так, что нарушает существующую функциональность, предыдущей функциональности будет поддерживаться не менее двух основных версий, когда это возможно перед удалением. Например, если функция берет три аргумента, и это число уменьшается до 2 в основной версии 10, трехаргументная версия продолжит работать до, как минимум, основной версии 12. Прошёл минимальный порог в двух версиях мы постараемся поддерживать обратную совместимость за пределами двух версий до тех пор, пока разработчики не почувствуют, что нагрузка на техническое обслуживание слишком высока, чтобы продолжать это делать.

### Поддерживаемые версии
- 10.x.y
- 9.x.y
- 8.x.y

### Конец жизни

Когда ветка выпуска достигает конца своего цикла опорной работы, серия будет устарела в Музее, и окончательный релиз будет создан. Эта версия добавит предупреждение, чтобы сообщить об использовании неподдерживаемой версии Electron.

Эти шаги должны помочь разработчикам приложений, когда они используют, становится неподдерживаемым, но без чрезмерно навязчивой для конечных пользователей.

Если заявка имеет исключительные обстоятельства и нуждается в на неподдерживаемой серии Electron, разработчики могут заглушить предупреждение о завершении после выхода из дистрибутива пакета `. son` `devDependencies`. Например, начиная с 1-6-х рядов закончился с конечной поддержкой 1.6. 8 релиз, разработчики могли бы остаться в 1-6-х рядах без предупреждений с `devDependency` из `"electron": 1. .0 - 1.6.17`.

## Поддерживаемые платформы

Следующие платформы поддерживаются Electron:

### macOS

Только 64-битные файлы предоставляются для macOS, и минимальная поддерживаемая версия macOS — macOS 10.10 (Yosemite).

### Windows

Поддерживаются Windows 7 и более поздние версии, более старые операционные системы не поддерживаются (и не работают).

Оба двоичных файла `ia32` (`x86`) и `x64` (`amd64`) предоставляются для Windows. [Electron 6.0.8 и выше добавьте встроенную поддержку Windows на Arm (`arm64`) устройствах](windows-arm.md). Запуск приложений, упакованных с предыдущими версиями, возможен с использованием бинарного файла ia32.

### Linux

Предварительные сборки `ia32` (`i686`) и `x64` (`amd64`) Electron основаны на Ubuntu 12.04, сборка `armv7l` собрана против ARM v7 с прошитым ABI и NEON для Debian Wheezy.

[До релиза Electron 2.0][arm-breaking-change], Electron также будет продолжать выпуск бинаркой сборки `armv7l` с простым `arm` суффиксом. Оба бинарных файла идентичны.

Будет ли готовая бинарная сборка включена в дистрибутив, зависит от того, включает ли дистрибутив библиотеки, с которыми связан Electron на компилирруемой платформе, поэтому гарантируется работа только в Ubuntu 12.04, но следующие платформы также проверяются, чтобы иметь возможность запускать прекомпилированные сборки Electron:

* Ubuntu 12.04 и новее
* Fedora 21
* Debian 8

[arm-breaking-change]: ../breaking-changes.md#duplicate-arm-assets