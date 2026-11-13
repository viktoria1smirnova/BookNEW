# Книжная лавка деканата — импорт книг из CSV

Производственная практика ПП.02.01 «Осуществление интеграции программных модулей»
(специальность 09.02.07 «Информационные системы и программирование»).

**Студент:** Смирнова Виктория
**Место практики:** деканат
**Тема:** разработка модуля импорта товаров в электронный магазин из CSV-файлов с хранением данных в коллекциях в памяти.
**Вариант:** 2 «Книги» (доп. поля `isbn`, `author`, `publisher`, `publication_year`).

Предметная область — книжная лавка деканата: учебники, методические пособия,
справочники и бланки, которые продаются студентам.

## Что умеет программа

- импорт CSV по пути к файлу или загрузкой файла через браузер;
- проверка данных и список ошибок с номерами строк;
- хранение книг в памяти (`Dictionary<string, Product>`, singleton);
- каталог: поиск по названию/автору, фильтр по категории, сортировка по цене и году;
- карточка книги, итоги (количество экземпляров и стоимость остатков);
- кодировки UTF-8 и Windows-1251, разделители `,` и `;`;
- очистка списка для повторной демонстрации.

## Запуск

Нужен .NET 8 SDK. Открыть `BookStoreCsvImport.sln` в Visual Studio и нажать F5,
или из командной строки:

```
dotnet run --project src/BookStoreCsvImport
```

Открыть http://localhost:5080 → «Импорт из CSV» → выбрать `books.csv` → «Импортировать».

Тесты: `dotnet test` (14 тестов xUnit).

## Формат CSV

```
sku,name,description,price,currency,category_name,stock,image_url,is_active,isbn,author,publisher,publication_year
2001,Основы программирования на C#,Учебник,1890,RUB,Учебники,12,/images/csharp.jpg,true,9785446100019,Иванов И.И.,Питер,2023
```

| Поле | Правило |
|------|---------|
| sku | обязательно, уникально (дубликаты пропускаются с ошибкой) |
| name | обязательно |
| price | число больше 0 |
| currency | пусто → RUB |
| category_name | пусто → «Без категории» |
| stock | целое число ≥ 0 |
| is_active | true / false / 1 / 0 |
| isbn | обязательно, 10 или 13 символов (дефисы допускаются) |
| author | обязательно |
| publisher | может быть пустым |
| publication_year | от 1900 до текущего года + 1 |

## Структура

```
src/BookStoreCsvImport/
  Controllers/ProductsController.cs   — каталог, карточка, импорт, загрузка, очистка
  Models/                             — Product, ImportResult, ImportError, ImportSettings, view-модели
  Services/
    InMemoryProductRepository.cs      — хранение в памяти
    ProductImportService.cs           — чтение файла и импорт
    BookRowValidator.cs               — проверка строки
    CsvSplitter.cs                    — разбор строки CSV
  Views/Products/                     — Index, Import, Details
  wwwroot/csv/                        — books.csv и тестовые файлы в папке test
tests/BookStoreCsvImport.Tests/       — тесты xUnit
```
