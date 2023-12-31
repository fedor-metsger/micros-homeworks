# Ответы на домашнее задание к занятию «Введение в микросервисы»

## Задача

Руководство крупного интернет-магазина, у которого постоянно растёт пользовательская база и количество заказов, рассматривает возможность переделки своей внутренней   ИТ-системы на основе микросервисов. 

Вас пригласили в качестве консультанта для оценки целесообразности перехода на микросервисную архитектуру. 

Опишите, какие выгоды может получить компания от перехода на микросервисную архитектуру и какие проблемы нужно решить в первую очередь.

## Решение

### Преимущества

Построение микросервисной архитектуры позволяет получить следующие преимущества:

1. Облегчает построение большой сложной системы, построенной с помощью различных технологий.
1. Построение системы с большим количеством интеграций.
1. Увеличение устойчивости системы к ошибкам. 
1. Приспособление системы к работе с часто меняющейся неоднородной нагрузкой,
облегчение масштабируемости.
1. Обеспечение работы с системой различных бизнес подразделений, построение системы,
отражающей структуру организации.
1. Обеспечение простоты развёртывания, замены отдельных компонент системы,
возможности внесения быстрых и частых изменений в систему

### Проблемы

1. Проблемы разработки:
   - Грамотное разбиение системы на сервисы (компоненты) и
     определение способов взаимодействия между компонентами.
   - Совместимость API различных компонентов системы.
   - Обеспечение нужного уровня безопасности.
   - Обеспечение версионирования артефактов.
   - Обеспечение автоматизации процессов сборки и тестирования компонентов.
   - Качественное документирование компонентов системы.
   - Создание правильной инфраструктуры разработки и способов взаимодействия между командами.
   

2. Проблемы эксплуатации:
   - Обеспечение мониторинга функционирования системы.
   - Генерация и сбор логов с отдельных компонентов.
   - Обеспечение централизованного управления настройками компонентов системы.
   - Обеспечение развития инфраструктуры эксплуатации в соответствии с развитием системы.






