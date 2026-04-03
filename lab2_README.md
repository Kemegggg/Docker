# Лабораторная работа №2

## Титульный лист
- ФИО: Попов Александр Евгеньевич
- Группа: АДЭУ-221
- Вариант: 10
- Тема данных: Stock Market
- Стек: Apache (Static Report) + Python (Pandas, NumPy, Matplotlib)

---

## 1. Цель работы

Целью лабораторной работы является разработка воспроизводимого аналитического инструмента:  
от генерации и анализа данных до упаковки результата в Docker-образ и запуска в изолированной среде.

---

## 2. Описание задачи

В рамках работы был реализован аналитический скрипт, который:

- генерирует синтетические данные фондового рынка;
- рассчитывает ключевые бизнес-метрики;
- строит график изменения цен;
- формирует HTML-отчет.

Далее отчет был упакован в Docker-образ на базе Apache и развернут как веб-страница.

---

## 3. Используемые данные

Используются синтетические данные по акциям:

- дата (date)
- тикер (ticker)
- цена открытия (open_price)
- цена закрытия (close_price)
- объем торгов (volume)

---

## 4. Расчет бизнес-метрик

В скрипте рассчитываются:

- средняя цена закрытия
- максимальная цена закрытия
- минимальная цена закрытия
- средний объем торгов
- дневное изменение цены

---

## 5. Структура проекта

```
bash
lab_02.1/
├── app/
│   ├── generate_report.py
│   ├── requirements.txt
│   └── output/
│       ├── index.html
│       └── chart.png
├── Dockerfile
├── .dockerignore
└── REPORT.md
```

## 1. Создание проекта

<img width="517" height="236" alt="image-33" src="https://github.com/user-attachments/assets/51d7856f-f599-4ec1-a802-adb69eb99241" />

## 2. Создание Python-скрипта

<img width="730" height="764" alt="image-34" src="https://github.com/user-attachments/assets/46cadc4e-f425-482d-a251-5bcc9e3efb52" />

```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from pathlib import Path

OUTPUT_DIR = Path("app/output")
OUTPUT_DIR.mkdir(parents=True, exist_ok=True)

np.random.seed(42)

dates = pd.date_range(start="2025-01-01", periods=30, freq="D")
open_prices = np.random.uniform(95, 105, len(dates)).round(2)
close_shift = np.random.uniform(-3, 3, len(dates)).round(2)
close_prices = (open_prices + close_shift).round(2)
volumes = np.random.randint(1000, 10000, len(dates))

df = pd.DataFrame({
    "date": dates,
    "ticker": "AAPL",
    "open_price": open_prices,
    "close_price": close_prices,
    "volume": volumes
})

avg_close = round(df["close_price"].mean(), 2)
max_close = round(df["close_price"].max(), 2)
min_close = round(df["close_price"].min(), 2)
avg_volume = int(df["volume"].mean())

df["daily_change"] = (df["close_price"] - df["open_price"]).round(2)

plt.figure(figsize=(10, 5))
plt.plot(df["date"], df["open_price"], label="Open Price")
plt.plot(df["date"], df["close_price"], label="Close Price")
plt.legend()
plt.xticks(rotation=45)
plt.tight_layout()
plt.savefig(OUTPUT_DIR / "chart.png")
plt.close()

table_html = df.to_html(index=False)

html = f"""
<html>
<head><title>Stock Report</title></head>
<body>
<h1>Stock Report (AAPL)</h1>
<p>Average: {avg_close}</p>
<p>Max: {max_close}</p>
<p>Min: {min_close}</p>
<p>Volume: {avg_volume}</p>
<img src="chart.png">
{table_html}
</body>
</html>
"""

with open(OUTPUT_DIR / "index.html", "w") as f:
    f.write(html)

print("DONE")
```

### 3. Создание виртуального окружения

<img width="517" height="236" alt="image-33" src="https://github.com/user-attachments/assets/e5259ef1-1040-4cba-9f01-e008394918a3" />

<img width="453" height="51" alt="image-35" src="https://github.com/user-attachments/assets/97886cd0-21f3-4c96-8f21-90d7d463eb18" />

### 4. Установка зависимостей

<img width="725" height="676" alt="image-36" src="https://github.com/user-attachments/assets/40e1467d-9470-48f7-9f7c-7c8fcbe2a564" />

### 5. Генерация отчета

<img width="1140" height="210" alt="image-37" src="https://github.com/user-attachments/assets/1ade2b68-ff8f-4d16-8bce-9a3b3d57f007" />

### 6. Проверка HTML

<img width="542" height="66" alt="image-38" src="https://github.com/user-attachments/assets/05d471d1-cb5a-4964-948b-5094b28ae461" />

## ОТЧЕТ HTML

<img width="1309" height="816" alt="image-39" src="https://github.com/user-attachments/assets/9dbb1153-7405-48b0-8c61-7302c87cd6a8" />
<img width="662" height="803" alt="image-40" src="https://github.com/user-attachments/assets/4ca01220-4da4-4886-8fe6-2184af603695" />

[Перейти к HTML отчету]( Stock_Market_Report.html )

### 7. Создание Dockerfile

<img width="473" height="173" alt="image-41" src="https://github.com/user-attachments/assets/7b06735c-af6b-462b-969f-f2b0973293c3" />

### 8. Сборка Docker-образа

<img width="793" height="613" alt="image-42" src="https://github.com/user-attachments/assets/6ca89bf6-0f58-420b-8bef-877f9dea3932" />

### 9. Запуск контейнера

<img width="780" height="34" alt="image-43" src="https://github.com/user-attachments/assets/2ee9c1e9-0db8-40a5-adc1-54b3a8f632eb" />

### 10. Проверка контейнера

<img width="792" height="95" alt="image-44" src="https://github.com/user-attachments/assets/f693a544-0fa2-4590-b127-16b26cc13ed1" />

### 11. Заключение

Поставленная цель лабораторной работы достигнута.  
Разработанный аналитический инструмент является воспроизводимым, так как может быть развернут на любой системе с использованием Docker.
