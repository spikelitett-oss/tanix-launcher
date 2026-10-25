# Tanix Launcher (Material You) для Android TV

## Сборка
1. Установите Android Studio (Koala 2024.1 или новее).
2. File → Open → папка TanixLauncher, дождитесь Gradle Sync (gradle wrapper Android Studio создаст сама).
3. Build → Build APK(s) → app/build/outputs/apk/debug/app-debug.apk

## Установка на приставку (Tanix W2 и др.)
1. Настройки → О устройстве → 7 раз нажать «Сборка» → Для разработчиков → «Отладка по USB/сети».
2. adb connect IP_ПРИСТАВКИ:5555
3. adb install -r app-debug.apk
4. Главным экраном: адрес первого запуска предложит это сам, либо
   adb shell cmd package set-home-activity com.tanix.launcher/.MainActivity

## Погода
Open-Meteo (бесплатно, без ключа). Яндекс.Погода и Gismeteo требуют платный/ограниченный ключ.

## TMDB в России
1. Нужен бесплатный ключ API v3: themoviedb.org → Settings → API. Сайт из России может быть закрыт —
   ключ можно получить один раз через VPN, дальше VPN не нужен.
2. Настройки → Рекомендации TMDB → «Включить прокси TMDB» (включено по умолчанию):
   лаунчер использует официальный домен api.tmdb.org и определяет IP через DNS-over-HTTPS
   (Cloudflare 1.1.1.1 / Google 8.8.8.8), минуя DNS провайдера.
3. «Проверить соединение» покажет, работает ли это у вашего провайдера.
4. Если не работает — поднимите своё зеркало на Cloudflare Workers (бесплатно) и впишите его адрес
   в «Своё зеркало»:

   export default {
     async fetch(req) {
       const u = new URL(req.url);
       const host = u.pathname.startsWith('/t/p/') ? 'https://image.tmdb.org' : 'https://api.themoviedb.org';
       return fetch(host + u.pathname + u.search);
     }
   }

## Что нового в 1.1
- Постеры (вертикальные) вместо большого баннера: название и мелкое описание под обложкой.
- Рекомендации — только российские фильмы и сериалы (язык оригинала ru, страна RU), свежие за 3 года.
- Автообновление рекомендаций раз в 4 дня (кэш на устройстве), ручное — в настройках.
- Кнопка «ТВ» рядом с настройками: открывает выбранное приложение (Lampa, ILTV…). Долгое нажатие — сменить.
- Иконка и баннер — «Tanix launcher».

## Готовый APK (без исходников)
Release-APK: Build → Generate Signed Bundle / APK → APK → release
(или в терминале: ./gradlew assembleRelease → app/build/outputs/apk/release/app-release.apk).
Файл можно просто отправить другому человеку и установить на приставку; код ему не нужен.

## Прокси TMDB (1.2)
Тип «Rootu (как в Lampa)» — по умолчанию. Берётся из плагина https://plugin.rootu.top/tmdb.js:
API — tmdb-api.rootu.top/3/, картинки — tmdb-img.rootu.top/t/p/. Плагин работает по http, поэтому в
res/xml/network_security_config.xml http разрешён только для этих двух адресов.
Лаунчер пробует http, затем https, и запоминает рабочий вариант.
Остальные типы: DNS-over-HTTPS и своё зеркало (Cloudflare Worker).
