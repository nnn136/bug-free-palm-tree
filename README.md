ФИО: Коротаева Полина Сергеевна

# Random Task Generator (Генератор случайных задач)

**Описание программы:**
Приложение "Random Task Generator" предназначено для генерации случайных задач из заранее определенного списка. Пользователь может генерировать новую задачу, а также просматривать историю сгенерированных задач. Программа включает функционал фильтрации по типу задачи и сохраняет историю в формате JSON, позволяя пользователю загружать её при следующем запуске приложения.

## Пошаговая инструкция

### Шаг 1: Создание списка предопределённых задач

Мы создадим предопределенный список задач, который будет использоваться для генерации случайных задач.

```python

import tkinter as tk
from tkinter import messagebox, ttk
import random
import json
import os

class RandomTaskGenerator:
    def __init__(self, master):
        self.master = master
        master.title("Random Task Generator")

        self.tasks = [
            {"task": "Прочитать статью", "type": "учёба"},
            {"task": "Сделать зарядку", "type": "спорт"},
            {"task": "Подготовить отчет", "type": "работа"},
            {"task": "Погулять с собакой", "type": "спорт"},
            {"task": "Изучить новый язык", "type": "учёба"},
            {"task": "Сделать уборку", "type": "работа"},
            {"task": "Написать блог", "type": "учёба"},
            {"task": "Пробежаться", "type": "спорт"},
            {"task": "Собрать документы", "type": "работа"}
        ]

        self.history = []

        # Кнопка генерирования задачи
        self.generate_button = tk.Button(master, text="Сгенерировать задачу", command=self.generate_task)
        self.generate_button.pack(pady=10)

        # Поле для отображения сгенерированной задачи
        self.task_label = tk.Label(master, text="", font=("Arial", 14))
        self.task_label.pack(pady=10)

        # Кнопка для фильтрации
        self.type_label = tk.Label(master, text="Фильтр по типу задачи:")
        self.type_label.pack()

        self.type_entry = tk.Entry(master)
        self.type_entry.pack(pady=5)

        self.filter_button = tk.Button(master, text="Фильтровать", command=self.filter_tasks)
        self.filter_button.pack(pady=5)

        # История задач
        self.history_label = tk.Label(master, text="История задач:")
        self.history_label.pack()

        self.history_listbox = tk.Listbox(master, width=50)
        self.history_listbox.pack(pady=10)

        self.load_history()

    def generate_task(self):
        task = random.choice(self.tasks)
        self.task_label.config(text=task["task"])
        self.history.append(task["task"])
        self.update_history()

    def update_history(self):
        self.history_listbox.delete(0, tk.END)
        for item in self.history:
            self.history_listbox.insert(tk.END, item)
        self.save_history()

    def filter_tasks(self):
        filter_type = self.type_entry.get().lower()
        self.history_listbox.delete(0, tk.END)
        for task in self.history:
            if filter_type in [t["type"] for t in self.tasks if t["task"] == task]:
                self.history_listbox.insert(tk.END, task)

    def load_history(self):
        if os.path.exists("history.json"):
            with open("history.json", "r") as f:
                self.history = json.load(f)
            self.update_history()

    def save_history(self):
        with open("history.json", "w") as f:
            json.dump(self.history, f)

if __name__ == "__main__":
    root = tk.Tk()
    app = RandomTaskGenerator(root)
    root.mainloop()
```

### Шаг 2: Генерация задачи

Кнопка "Сгенерировать задачу" вызывает метод `generate_task`, который выбирает случайную задачу из списка и отображает её на экране.

### Шаг 3: Отображение истории

История сгенерированных задач отображается в `Listbox`. Каждое новое задание добавляется в историю автоматически.

### Шаг 4: Фильтрация задач

Функционал фильтрации реализуется в методе `filter_tasks`, который позволяет фильтровать задачи по заданному типу.

### Шаг 5: Сохранение истории в JSON

История задач сохраняется в файл `history.json` с помощью методов `load_history` и `save_history`.

### Шаг 6: Проверка корректности ввода

В данном приложении не требуется дополнительных пользовательских вводов, кроме