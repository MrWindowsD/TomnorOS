![](https://github.com/MrWindowsD/TomnorOS/blob/main/image/header.gif)

# DPI — медленный YouTube?

В принципе про обход замедления YouTube уже много кто рассказал. Разберём частные случае и полёты.

## Что такое DPI?

**DPI** (Deep packet inspection) — это в целом мощная и крайне полезная технология, которую применяют во множестве IT компанией для управления трафиком:

Почему сетевой администратор всегда был хорошим детективом?

Потому что он использовал DPI — «глубокую проверку пакетов»! Он мог заглянуть в каждую «коробочку» данных, чтобы выяснить, кто на самом деле скрывается за маской «видеопотока» или «онлайн-игры».
Однажды он поймал пакет, который пытался выдать себя за обычный трафик. Он смотрит на него и говорит: «Эй, ты не просто "веб-страница", ты же "стриминг"! У тебя тут целая вечеринка без приглашения!»
Так что, если вы когда-нибудь почувствуете, что ваши данные под контролем, знайте — это не просто администратор, это настоящий шпион в мире сетей.

Другими словами данная технология позволяет не только перехватывать и анализировать пакеты, но и контролировать трафик, к примеру для того чтобы оптимизировать работу сетей внутри какой-то инфраструктуры. DPI позволяет работать с пакетами:
1. Ограничить скорость, если она требуется в другом месте — оптимизация трафика
2. Перенаправить — оптимизация трафика
3. Передать получателю
4. Заблокировать — меры безопасности сетей

Кроме того DPI позволяет проводить эвристический анализ пакетов — это как попытка угадать, что такое "секретная" информация, которую скрывает пакет данных. Это как игра в "угадай, что в коробке", только вместо коробки - пакет данных, а вместо угадывания - сложные алгоритмы и правила.

Однако, данную технологию можно использовать как мы уже поняли для блокировки сайтов и сервисов, как это делает ТСПУ. И возникает резонный вопрос, а что с этим делать? И точно ли это делает ТСПУ? Или может сам google?

## Немного подушим

Посомтрим на логи самого YouTube через Wireshark, и обнаружим то, что пакеты просто блокируются и трафик не проходит должным образом — падает скорость. Но как это происходит, если YT видит толь raw ip пакеты? И тут подключается DPI, который на уровне TLS по [SNI](https://en.wikipedia.org/wiki/Server_Name_Indication), который не шифруется определяет к какому сайту мы хотим подключиться, на этому уровне и происходит замедление. Мы можем проверить это при помощи команды 
```bash
curl --connect-to ::speedtest.selectel.ru https://manifest.googlevideo.com/100MB -k -o/dev/null
```
где
1) Мы можем подменить SNI
2) -k флаг отключает проверку сертификатов
3) Мы получаем низкую скорость или вовсе ошибку при подключение к googlevideo.com
4) Но если поменять имя, то скорость возвращается обратно

Это подтверждает замедление YT со стороны Роскомнадзора. Для обхода нам понадобиться Фейковый ClientHello и разбивка пакетов.

## Что можно сделать?

1. Не рекомендую устанавливать сторонний VPN
2. Развернуть свой VPN на ПК или [роутере](https://github.com/Waujito/youtubeUnblock), например при помощи [OpenVPN](https://habr.com/ru/articles/78101/). Где взять VPN сервер для подключения? [Тут](https://www.vpnjantit.com/free-openvpn)
3. Установить [GoodbyeDPI](https://github.com/ValdikSS/GoodbyeDPI) от ValdikSS. Отлично работает на системах Windows
4. Прокси [Byedpi](https://github.com/hufrea/byedpi) от hufrea
5. Выше представлены самые оптимальные варианты. Можно установить прокси или какое-нибудь расширение для браузера, но это та ещё морока, к тому же часто платная, потому я такое движение не рекомендую

![](https://github.com/MrWindowsD/TomnorOS/blob/main/image/hr_00000.png)

## Прочее
1. Прокси под [Android](https://github.com/dovecoteescapee/ByeDPIAndroid) от hufrea
2. [Обсуждение темы](https://ntc.party/t/%D0%BE%D0%B1%D1%81%D1%83%D0%B6%D0%B4%D0%B5%D0%BD%D0%B8%D0%B5-%D0%B7%D0%B0%D0%BC%D0%B5%D0%B4%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5-youtube-%D0%B2-%D1%80%D0%BE%D1%81%D1%81%D0%B8%D0%B8/8074)
