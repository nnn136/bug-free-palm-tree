ФИО: Белинина Яна Сергеевна

import tkinter as tk
from tkinter import ttk, messagebox
import json
import random
import string
import os

class PasswordGenerator:
    def __init__(self, root):
        self.root = root
        self.root.title("Random Password Generator")
        self.root.geometry("600x500")

        # Переменная для длины пароля
        self.length = tk.IntVar(value=12)
        # Переменные для выбора символов
        self.use_digits = tk.BooleanVar(value=True)
        self.use_letters = tk.BooleanVar(value=True)
        self.use_special = tk.BooleanVar(value=False)

        self.history = []
        self.load_history()

        self.create_widgets()

    def create_widgets(self):
        # Ползунок длины пароля
        ttk.Label(self.root, text="Длина пароля:").pack(pady=5)
        length_slider = ttk.Scale(
            self.root,
            from_=4,
            to=32,
            orient="horizontal",
            variable=self.length
        )
        length_slider.pack(pady=5, fill="x", padx=20)

        length_label = ttk.Label(self.root, textvariable=self.length)
        length_label.pack()

        # Чекбоксы для выбора символов
        ttk.Checkbutton(
            self.root,
            text="Цифры (0-9)",
            variable=self.use_digits
        ).pack(anchor="w", padx=20)
        ttk.Checkbutton(
            self.root,
            text="Буквы (A-Z, a-z)",
            variable=self.use_letters
        ).pack(anchor="w", padx=20)
        ttk.Checkbutton(
            self.root,
            text="Спецсимволы (!@#$%)",
            variable=self.use_special
        ).pack(anchor="w", padx=20)

        # Кнопка генерации
        generate_btn = ttk.Button(
            self.root,
            text="Сгенерировать пароль",
            command=self.generate_password
        )
        generate_btn.pack(pady=10)

        # Поле отображения пароля
        self.password_var = tk.StringVar()
        password_entry = ttk.Entry(
            self.root,
            textvariable=self.password_var,
            state="readonly",
            font=("Courier", 12)
        )
        password_entry.pack(fill="x", padx=20, pady=5)

        # Таблица истории
        columns = ("ID", "Пароль", "Длина", "Символы")
        self.tree = ttk.Treeview(self.root, columns=columns, show="headings")

        for col in columns:
            self.tree.heading(col, text=col)
            self.tree.column(col, width=100)

        self.tree.pack(fill="both", expand=True, padx=20, pady=10)

        # Кнопка очистки истории
        clear_btn = ttk.Button(
            self.root,
            text="Очистить историю",
            command=self.clear_history
        )
        clear_btn.pack(pady=5)
    def generate_password(self):
        # Проверка корректности ввода
        if not self.validate_input():
            return

        # Формирование пула символов
        chars = ""
        if self.use_digits.get():
            chars += string.digits
        if self.use_letters.get():
            chars += string.ascii_letters
        if self.use_special.get():
            chars += "!@#$%^&*"

        if not chars:
            messagebox.showerror("Ошибка", "Выберите хотя бы один тип символов!")
            return

        # Генерация пароля
        password = ''.join(random.choice(chars) for _ in range(self.length.get()))
        self.password_var.set(password)

        # Добавление в историю
        self.add_to_history(password)
    def load_history(self):
        try:
            if os.path.exists("password_history.json"):
                with open("password_history.json", "r", encoding="utf-8") as f:
                    self.history = json.load(f)
                self.update_history_table()
        except Exception as e:
            print(f"Ошибка загрузки истории: {e}")

    def save_history(self):
        try:
            with open("password_history.json", "w", encoding="utf-8") as f:
                json.dump(self.history, f, ensure_ascii=False, indent=2)
        except Exception as e:
            print(f"Ошибка сохранения истории: {e}")

    def add_to_history(self, password):
        entry = {
            "id": len(self.history) + 1,
            "password": password,
            "length": self.length.get(),
            "characters": self.get_char_types()
        }
        self.history.append(entry)
        self.save_history()
        self.update_history_table()

    def update_history_table(self):
        # Очистка таблицы
        for item in self.tree.get_children():
            self.tree.delete(item)

        # Заполнение таблицы
        for entry in self.history[-50:]:  # Последние 50 записей
            self.tree.insert("", "end", values=(
                entry["id"],
                entry["password"],
                entry["length"],
                entry["characters"]
            ))

    def clear_history(self):
        self.history = []
        self.save_history()
        self.update_history_table()
    def validate_input(self):
        length = self.length.get()
        if length < 4:
            messagebox.showerror("Ошибка", "Минимальная длина пароля — 4 символа!")
            return False
        if length > 32:
            messagebox.showerror("Ошибка", "Максимальная длина пароля — 32 символа!")
            return False
        return True

    def get_char_types(self):
        types = []
        if self.use_digits.get(): types.append("Цифры")
        if self.use_letters.get(): types.append("Буквы")
        if self.use_special.get(): types.append("Спецсимволы")
        return ", ".join(types)
if __name__ == "__main__":
    root = tk.Tk()
    app = PasswordGenerator(root)
    root.mainloop()


Random Password Generator — приложение для генерации безопасных паролей с настраиваемыми параметрами и сохранением истории.

Возможности: настройка длины пароля (4–32 символа);

выбор типов символов (цифры, буквы, спецсимволы);

генерация случайных паролей;

сохранение истории последних 50 паролей;

экспорт истории в JSON;

интуитивно понятный интерфейс.

Примеры использования: Генерация простого пароля (12 символов, только цифры и буквы).

Создание сложного пароля (20 символов, с спецсимволами).

Просмотр истории и копирование ранее сгенерированных паролей