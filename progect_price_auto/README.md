# Проект APP (Auto Price Predictor)

Проект направлен на разработку модели машинного обучения для предсказания рыночной стоимости автомобилей на основе их характеристик. Это позволит пользователям сервиса по продаже автомобилей с пробегом оценить стоимость своего авто и повысит привлекательность сервиса для новых клиентов.

## Описание проекта

В рамках проекта проводится анализ набора данных об автомобилях: их технических характеристик, истории эксплуатации, популярности марок и моделей. Исследуется влияние различных признаков (например, пробега, мощности, типа топлива и т.д.) на конечную стоимость автомобиля. Главная цель — построить точную, быструю и интерпретируемую модель, которая будет использоваться в реальном приложении.

## Цели проекта

1. Подготовить и исследовать данные об автомобилях.
2. Построить и сравнить модели регрессии.
3. Выбрать наилучшую модель по качеству и скорости предсказания.
4. Интерпретировать модель и выделить наиболее значимые признаки.

## Описание данных

Файл `autos.csv` содержит информацию об автомобилях, выставленных на продажу:

| Название признака     | Описание                                                                     |
|------------------------|------------------------------------------------------------------------------|
| `DateCrawled`          | Дата скачивания анкеты из базы                                              |
| `VehicleType`          | Тип кузова (например, sedan, suv и т.д.)                                    |
| `RegistrationYear`     | Год регистрации автомобиля                                                   |
| `Gearbox`              | Тип коробки передач (manual / auto)                                          |
| `Power`                | Мощность двигателя (л.с.)                                                    |
| `Model`                | Модель автомобиля                                                            |
| `Kilometer`            | Пробег автомобиля (в км)                                                     |
| `RegistrationMonth`    | Месяц регистрации автомобиля                                                 |
| `FuelType`             | Тип топлива (например, бензин, дизель и т.д.)                                |
| `Brand`                | Марка автомобиля                                                             |
| `Repaired`             | Была ли машина в ремонте (`yes`/`no`)                                        |
| `DateCreated`          | Дата создания объявления                                                     |
| `NumberOfPictures`     | Количество фотографий (всегда 0 в датасете)                                  |
| `PostalCode`           | Почтовый индекс владельца анкеты                                             |
| `LastSeen`             | Дата последней активности пользователя                                       |
| **`Price`**            | **Целевая переменная — цена автомобиля (в евро)**                            |

## Используемые библиотеки

- `pandas`, `numpy` — для работы с данными 
- `matplotlib`, `seaborn` — для визуализации данных  
- `scikit-learn` — для построения моделей и пайплайнов  
- `lightgbm` — градиентный бустинг  
- `phik`, `shap` — для интерпретации моделей

---

## Результаты

### Предобработка данных

- Выполнены следующие действия:
  - Переименование столбцов;
  - Обработка неявных дубликатов;
  - Заполнение пропущенных значений меткой `'unknown'`;
  - Обработка аномальных значений;
  - Удаление неинформативных признаков.

- Количество полных дубликатов в исходном датафрейме: **45 040**

- Пропуски в данных:

| Признак       | Кол-во пропусков | Процент пропусков |
|:--------------|-----------------:|------------------:|
| `Repaired`    |          71 154  |           20.08%  |
| `VehicleType` |          37 490  |           10.58%  |
| `FuelType`    |          32 895  |            9.28%  |
| `Gearbox`     |          19 833  |            5.60%  |
| `Model`       |          19 705  |            5.56%  |

---

### EDA (исследовательский анализ)

- Сформирован портрет типичного автомобиля для перепродажи:
  - Бензиновый `Volkswagen`, `BMW` или `Opel`
  - Средняя цена: **1500 евро**
  - Год регистрации: **2000**
  - Мощность: **75 л.с.**
  - Коробка передач: **ручная**
  - Состояние: **не был в ремонте**

---

### Моделирование

- Исследованы 4 модели:
  - `LightGBM`
  - `KNeighborsRegressor`
  - `DecisionTreeRegressor`
  - `LinearRegression`

| Модель                | Время обучения (сек) | Время предсказания (сек) | RMSE            |
|-----------------------|---------------------:|---------------------------:|-----------------:|
| `LGBMRegressor`       | **60.68**            | **5.34**                   | **1623.38**      |
| `KNeighborsRegressor` | 0.08                 | 0.92                       | 1919.73          |
| `DecisionTreeRegressor` | 0.38               | 0.03                       | 2049.33          |
| `LinearRegression`    | 1.53                 | 0.11                       | 2322.11          |

- **Выбор модели:**  
  - Наилучшее качество и приемлемая скорость — у `LGBMRegressor`
  - RMSE на тестовой выборке: **1558.15**
  - Наиболее важный признак: `registration_year`  
  - Наименее значимый: `fuel_type`

- Дополнительно:
  - `KNeighborsRegressor` — легко интерпретируемая и быстрая модель, пригодна при высоких требованиях к скорости.
  - `LinearRegression` и `DecisionTreeRegressor` — удовлетворяют условиям по RMSE, но уступают по метрике.

---

### Рекомендации для бизнеса

1. Улучшить сбор и валидацию данных (исключить дубли, неверные значения, пропуски).
2. Сделать ключевые поля обязательными для заполнения.
3. Исключить неинформативные признаки из анкеты (например, `NumberOfPictures`).
4. Добавить новые признаки, потенциально влияющие на цену:
   - Условия эксплуатации
   - Частота ТО
   - Количество владельцев
5. Расширить пространство подбираемых гиперпараметров для моделей.
6. Применить **feature engineering** для создания дополнительных информативных признаков.