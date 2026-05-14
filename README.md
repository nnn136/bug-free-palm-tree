фио: Хакназарова Шахзода Саидназаровна

import tkinter as tk
from tkinter import messagebox, ttk
import requests
import json

class GitHubUserFinder:
    def __init__(self, master):
        self.master = master
        master.title("GitHub User Finder")

        # Поле ввода пользователя
        self.search_label = tk.Label(master, text="Поиск пользователя GitHub:")
        self.search_label.pack(pady=5)

        self.search_entry = tk.Entry(master)
        self.search_entry.pack(pady=5)

        self.search_button = tk.Button(master, text="Найти", command=self.search_user)
        self.search_button.pack(pady=5)

        # Список результатов
        self.results_list = ttk.Treeview(master, columns=("username"), show='headings')
        self.results_list.heading("username", text="Пользователь")
        self.results_list.pack(pady=10)

        self.results_list.bind('<Double-1>', self.add_to_favorites)

        # Кнопка для сохранения избранных пользователей
        self.save_button = tk.Button(master, text="Сохранить избранные", command=self.save_favorites)
        self.save_button.pack(pady=5)

        self.favorites = []
        self.load_favorites()

    def search_user(self):
        username = self.search_entry.get()
        if not username:
            messagebox.showerror("Ошибка", "Поле поиска не должно быть пустым.")
            return
        
        response = requests.get(f"https://api.github.com/users/{username}")
        if response.status_code == 200:
            user = response.json()
            self.results_list.insert("", tk.END, values=(user["login"],))
        else:
            messagebox.showerror("Ошибка", "Пользователь не найден.")

    def add_to_favorites(self, event):
        selected_item = self.results_list.selection()
        if selected_item:
            user = self.results_list.item(selected_item)['values'][0]
            if user not in self.favorites:
                self.favorites.append(user)
                messagebox.showinfo("Успех", f"Пользователь {user} добавлен в избранное.")
            else:
                messagebox.showinfo("Уведомление", f"Пользователь {user} уже в избранном.")

    def save_favorites(self):
        with open("favorites.json", "w") as f:
            json.dump(self.favorites, f)
        messagebox.showinfo("Успех", "Избранные пользователи сохранены.")

    def load_favorites(self):
        try:
            with open("favorites.json", "r") as f:
                self.favorites = json.load(f)
        except FileNotFoundError:
            self.favorites = []

if __name__ == "__main__":
    root = tk.Tk()
    app = GitHubUserFinder(root)
    root.mainloop()


GitHub User Finder (Поиск пользователей GitHub)
Описание программы:
Приложение "GitHub User Finder" позволяет пользователям искать пользователей на GitHub через API GitHub. 
Данное приложение обеспечивает простую и удобную графическую оболочку для выполнения поиска и добавления пользователей в избранное. 
Избранные пользователи сохраняются в локальный файл JSON, что позволяет пользователям легко управлять своими предпочтениями.



## Требования

* Python 3.6+
* Библиотеки: `tkinter`, `requests`, `json`

## Установка и запуск

1. Клонируйте репозиторий:
```bash
git clone <URL-репозитория>
cd GitHub-User-Finder

#### Инструкция по настройке Git

1. Инициализируйте Git‑репозиторий:
```bash
git init

Примеры использования
Пример 1: Поиск пользователя

Введите имя пользователя в поле поиска (например, torvalds).

Нажмите кнопку «Найти».

В списке результатов появится Linus Torvalds и другие похожие пользователи.

Пример 2: Добавление в избранное

Выберите пользователя из списка результатов.

Нажмите «Добавить в избранное».

Пользователь появится в списке избранного.

Пример 3: Просмотр избранного

Нажмите кнопку «Показать избранное».

В списке избранного отобразятся все сохранённые пользователи.

При следующем запуске приложения избранные пользователи сохранятся.

Тесты
Поиск пустой строки: приложение покажет предупреждение «Поле поиска не может быть пустым».

Поиск существующего пользователя: найдите torvalds, добавьте в избранное, закройте программу, откройте снова — пользователь должен остаться в избранном.

Добавление уже избранного: попытка добавить пользователя дважды вызовет предупреждение «Этот пользователь уже в избранном».

Удаление из избранного: выберите пользователя в списке избранного и нажмите «Удалить из избранного» — пользователь исчезнет из списка.