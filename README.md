# Домашнее задание к занятию 13.1. «Уязвимости и атаки на информационные системы» - `Виноградова Татьяна`

### Задание 1

Скачайте и установите виртуальную машину Metasploitable: https://sourceforge.net/projects/metasploitable/.

Это типовая ОС для экспериментов в области информационной безопасности, с которой следует начать при анализе уязвимостей.

Просканируйте эту виртуальную машину, используя **nmap**.

Попробуйте найти уязвимости, которым подвержена эта виртуальная машина.

Сами уязвимости можно поискать на сайте https://www.exploit-db.com/.

Для этого нужно в поиске ввести название сетевой службы, обнаруженной на атакуемой машине, и выбрать подходящие по версии уязвимости.

Ответьте на следующие вопросы:

- Какие сетевые службы в ней разрешены?
- Какие уязвимости были вами обнаружены? (список со ссылками: достаточно трёх уязвимостей)
---
#### Сетевые службы:
![image](https://github.com/LadyInRed22/13.1_Vulnerabilities/assets/103531664/85903090-7242-4f67-9f24-11e407ba0431)
---
#### Уязвимости:

##### 1. [vsftpd 2.3.4](https://bdu.fstec.ru/vul/2015-02948)
##### 2. VNC - не нашла на https://bdu.fstec.ru/ и https://www.exploit-db.com/, но вот скриншот:
![image](https://user-images.githubusercontent.com/103531664/221424643-6b79ad54-9a1f-4e4a-bcff-f9f15a2c62a1.png)
##### 3. netbios-ssn - не нашла на https://bdu.fstec.ru/ и https://www.exploit-db.com/, но вот скриншот:
![image](https://user-images.githubusercontent.com/103531664/221426920-87c5d600-98af-458c-a607-84312ef58a47.png)
##### 4. postgresql - не нашла на https://bdu.fstec.ru/ и https://www.exploit-db.com/, но вот скриншот:
![image](https://user-images.githubusercontent.com/103531664/221927679-f2abffdd-d4bf-4064-b542-c99d27c22b11.png)


### Задание 2

Проведите сканирование Metasploitable в режимах SYN, FIN, Xmas, UDP.

Запишите сеансы сканирования в Wireshark.

Ответьте на следующие вопросы:

- Чем отличаются эти режимы сканирования с точки зрения сетевого трафика?
- Как отвечает сервер?
---
#### Сканирование SYN:

Метод TCP SYN — наиболее популярен, используется в 95% случаев. Его называют сканированием с установкой полуоткрытого соединения, так как соединение не устанавливается до конца. На исследуемый порт посылается сообщение SYN, затем идет ожидание ответа, на основании которого определяется статус порта. Ответы SYN/ACK говорят о том, что порт прослушивается (открыт), а ответ RST говорит о том, что не прослушивается.

##### Порт открыт:

![image](https://user-images.githubusercontent.com/103531664/221934740-a5c3b07c-9320-4746-b228-746df40f8c56.png)

##### Порт закрыт:

![image](https://user-images.githubusercontent.com/103531664/221934893-28aa4a8b-8f2e-4da4-bb88-759aa65495a6.png)

#### Сканирование FIN:

![image](https://user-images.githubusercontent.com/103531664/221942863-461cbded-8bd6-4a77-a6af-ea15ebccea71.png)

Методы FIN и Xmas заключаются в отправке пакетов с отключенными флагами в заголовке TCP. При FIN-сканировании устанавливается бит TCP FIN, а в Xmas-сканировании устанавливаются флаги FIN, PSH и URG. Хотя, при FIN-сканировании, как видно на скрине выше, в одном пакете тоже были установлены флаги FIN, PSH и URG.

#### Сканирование Xmas:

![image](https://user-images.githubusercontent.com/103531664/221943810-61097978-bcc9-4fc2-95b7-6f28e901b499.png)
![image](https://user-images.githubusercontent.com/103531664/221944248-4974910e-8e7a-48c1-8974-a96c969a8be7.png)


#### Сканирование UDP:

UDP-сканирование медленнее и сложнее, чем TCP-сканирование. Для определения статуса порта посылается пустой UDP-заголовок, и если в ответ приходит ошибка достижимости ICMP Destination Unreachable с кодом Destination port unreachable, это значит, что порт закрыт; другие ошибки достижимости ICMP (Destination host unreachable, Destination protocol unreachable, Network administratively prohibited, Host administratively prohibited, Communication administratively prohibited) означают, что порт фильтруется. Если порт отвечает UDP-пакетом, значит, он открыт. Из-за специфики UDP и потери пакетов запросы повторяются несколько раз, обычно три и более.

![image](https://user-images.githubusercontent.com/103531664/221953252-80225254-fa4b-4574-b80a-3daa9c10f172.png)

![image](https://user-images.githubusercontent.com/103531664/221958084-dc07f5e7-d725-4c3f-b8a5-602d107abf17.png)

---

Сервер не всегда отвечает однозначно, закрыт порт или фильтруется. При SYN-сканировании, если ответ от сервера - пакет с флагами SYN/ACK, то порт считается открытым, если пакет с флагом RST — считается закрытым. При FIN-сканировании, если ответ от сервера пакет с флагом RST - порт считается закрытым, если ответа нет - то открытым (как и при Xmas-сканировании, только, в отличии от FIN, в Xmas пакете установлены флаги FIN, PSH и URG). При UDP-сканировании сканируются только udp-порты, при получении ICMP-пакета с ошибкой unreachable error, порт считается закрытым, при получении пакетов с другими ошибками порт считается отфильтрованным, при получении  UDP-пакета порт считается открытым.
