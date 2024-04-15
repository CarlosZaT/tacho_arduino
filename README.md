[![latest](https://img.shields.io/github/v/release/GyverLibs/Tachometer.svg?color=brightgreen)](https://github.com/GyverLibs/Tachometer/releases/latest/download/Tachometer.zip)
[![PIO](https://badges.registry.platformio.org/packages/gyverlibs/library/Tachometer.svg)](https://registry.platformio.org/libraries/gyverlibs/Tachometer)
[![Foo](https://img.shields.io/badge/Website-AlexGyver.ru-blue.svg?style=flat-square)](https://alexgyver.ru/)
[![Foo](https://img.shields.io/badge/%E2%82%BD$%E2%82%AC%20%D0%9D%D0%B0%20%D0%BF%D0%B8%D0%B2%D0%BE-%D1%81%20%D1%80%D1%8B%D0%B1%D0%BA%D0%BE%D0%B9-orange.svg?style=flat-square)](https://alexgyver.ru/support_alex/)
[![Foo](https://img.shields.io/badge/README-ENGLISH-blueviolet.svg?style=flat-square)](https://github-com.translate.goog/GyverLibs/Tachometer?_x_tr_sl=ru&_x_tr_tl=en)  

[![Foo](https://img.shields.io/badge/ПОДПИСАТЬСЯ-НА%20ОБНОВЛЕНИЯ-brightgreen.svg?style=social&logo=telegram&color=blue)](https://t.me/GyverLibs)

# Tachometer
Библиотека тахометра для Arduino без привязки к железу
- Вызывай tick() в прерывании по фронту
- Забирай getRPM() частоту в об/мин
- Забирай getHz() частоту в Гц
- Забирай getUs() период в мкс
- Встроенный таймаут отсутствия сигнала
- Встроенное усреднение по n оборотам

### Совместимость
Совместима со всеми Arduino платформами (используются Arduino-функции)

## Содержание
- [Установка](#install)
- [Инициализация](#init)
- [Использование](#usage)
- [Пример](#example)
- [Версии](#versions)
- [Баги и обратная связь](#feedback)

<a id="install"></a>
## Установка
- Библиотеку можно найти по названию **Tachometer** и установить через менеджер библиотек в:
    - Arduino IDE
    - Arduino IDE v2
    - PlatformIO
- [Скачать библиотеку](https://github.com/GyverLibs/Tachometer/archive/refs/heads/main.zip) .zip архивом для ручной установки:
    - Распаковать и положить в *C:\Program Files (x86)\Arduino\libraries* (Windows x64)
    - Распаковать и положить в *C:\Program Files\Arduino\libraries* (Windows x32)
    - Распаковать и положить в *Документы/Arduino/libraries/*
    - (Arduino IDE) автоматическая установка из .zip: *Скетч/Подключить библиотеку/Добавить .ZIP библиотеку…* и указать скачанный архив
- Читай более подробную инструкцию по установке библиотек [здесь](https://alexgyver.ru/arduino-first/#%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0_%D0%B1%D0%B8%D0%B1%D0%BB%D0%B8%D0%BE%D1%82%D0%B5%D0%BA)
### Обновление
- Рекомендую всегда обновлять библиотеку: в новых версиях исправляются ошибки и баги, а также проводится оптимизация и добавляются новые фичи
- Через менеджер библиотек IDE: найти библиотеку как при установке и нажать "Обновить"
- Вручную: **удалить папку со старой версией**, а затем положить на её место новую. "Замену" делать нельзя: иногда в новых версиях удаляются файлы, которые останутся при замене и могут привести к ошибкам!


<a id="init"></a>
## Инициализация
```cpp
Tachometer tacho;
```

<a id="usage"></a>
## Использование
```cpp
void tick();                    // тикер - вызывать в прерывании по фронту
void setWindow(uint8_t window); // установка количества оборотов для усреднения (по умолч 10)
void setTimeout(uint16_t tout); // таймаут прерываний (мс), после которого считается, что вращение прекратилось (по умолч 1000)

uint32_t getRPM();              // получить обороты в минуту
float getHz();                  // получить герцы
uint32_t getUs();               // получить период в мкс
```

### Компьютерный вентилятор
При подключении тахометра компьютерного вентилятора рекомендуется использовать внешнюю подтяжку 4.7-10 кОм к VCC. Встроенного PULLUP не хватает, иногда прилетают помехи!  
![](/docs/fan.png)

<a id="example"></a>
## Пример
Остальные примеры смотри в **examples**!
```cpp
// измеряем обороты вентилятора от пк
#define TACH_PIN 2    // пин тахометра (желательна внешняя подтяжка 4.7к к VCC)

#include <Tachometer.h>
Tachometer tacho;

void setup() {
  Serial.begin(9600);

  // пин тахометра вентилятора подтягиваем к VCC
  pinMode(TACH_PIN, INPUT_PULLUP);

  // настраиваем прерывание
  attachInterrupt(0, isr, FALLING);

  //tacho.setWindow(20);    // установка количества тиков для счёта времени (по умолч 10)
  //tacho.setTimeout(2000); // таймаут прерываний (мс), после которого считается что вращение прекратилось
}

// обработчик прерывания
void isr() {
  tacho.tick();   // сообщаем библиотеке об этом
}

void loop() {
  // выводим 10 раз в секунду
  static uint32_t tmr;
  if (millis() - tmr > 100) {
    tmr = millis();
    Serial.println(tacho.getRPM());     // RPM
    //Serial.println(tacho.getHz());    // Hz
    //Serial.println(tacho.Us());       // us
  }
}
```

<a id="versions"></a>
## Версии
- v1.1
- v1.2
- v1.3 - улучшена стабильность, уменьшен вес кода

<a id="feedback"></a>
## Баги и обратная связь
При нахождении багов создавайте **Issue**, а лучше сразу пишите на почту [alex@alexgyver.ru](mailto:alex@alexgyver.ru)  
Библиотека открыта для доработки и ваших **Pull Request**'ов!


При сообщении о багах или некорректной работе библиотеки нужно обязательно указывать:
- Версия библиотеки
- Какой используется МК
- Версия SDK (для ESP)
- Версия Arduino IDE
- Корректно ли работают ли встроенные примеры, в которых используются функции и конструкции, приводящие к багу в вашем коде
- Какой код загружался, какая работа от него ожидалась и как он работает в реальности
- В идеале приложить минимальный код, в котором наблюдается баг. Не полотно из тысячи строк, а минимальный код
