ФИО: Буйвалова Анастасия Николаевна


# Movie Library (Личная кинотека)

**Описание программы:**
Приложение "Movie Library" предназначено для хранения информации о фильмах. Пользователи могут добавлять фильмы, фильтровать их по жанру и году выпуска, а также сохранять и загружать данные в формате JSON. Программа имеет простой и интуитивно понятный графический интерфейс.

## Пошаговая инструкция

### Шаг 1: Создание интерфейса

Для создания графического интерфейса мы можем использовать библиотеку Tkinter. Ниже представлен пример кода для реализации интерфейса:

```python


import tkinter as tk
from tkinter import messagebox, ttk
import json
import os

class MovieLibrary:
    def __init__(self, master):
        self.master = master
        master.title("Movie Library")

        # Поля ввода
        tk.Label(master, text="Название:").grid(row=0, column=0)
        self.title_entry = tk.Entry(master)
        self.title_entry.grid(row=0, column=1)

        tk.Label(master, text="Жанр:").grid(row=1, column=0)
        self.genre_entry = tk.Entry(master)
        self.genre_entry.grid(row=1, column=1)

        tk.Label(master, text="Год выпуска:").grid(row=2, column=0)
        self.year_entry = tk.Entry(master)
        self.year_entry.grid(row=2, column=1)

        tk.Label(master, text="Рейтинг:").grid(row=3, column=0)
        self.rating_entry = tk.Entry(master)
        self.rating_entry.grid(row=3, column=1)

        self.add_button = tk.Button(master, text="Добавить фильм", command=self.add_movie)
        self.add_button.grid(row=4, columnspan=2, pady=10)

        # Таблица для отображения фильмов
        self.movie_table = ttk.Treeview(master, columns=("title", "genre", "year", "rating"), show='headings')
        self.movie_table.heading("title", text="Название")
        self.movie_table.heading("genre", text="Жанр")
        self.movie_table.heading("year", text="Год выпуска")
        self.movie_table.heading("rating", text="Рейтинг")
        self.movie_table.grid(row=5, columnspan=2, pady=10)

        # Поле для фильтрации
        tk.Label(master, text="Фильтр по жанру:").grid(row=6, column=0)
        self.filter_genre_entry = tk.Entry(master)
        self.filter_genre_entry.grid(row=6, column=1)

        self.filter_button = tk.Button(master, text="Фильтровать", command=self.filter_movies)
        self.filter_button.grid(row=7, columnspan=2, pady=5)

        # Инициализация списка фильмов
        self.movies = []
        self.load_movies()

    def add_movie(self):
        title = self.title_entry.get()
        genre = self.genre_entry.get()
        year_str = self.year_entry.get()
        rating_str = self.rating_entry.get()

        # Проверка корректности ввода
        try:
            year = int(year_str)
            rating = float(rating_str)
            if rating < 0 or rating > 10:
                raise ValueError("Рейтинг должен быть от 0 до 10.")
        except ValueError as e:
            messagebox.showerror("Ошибка", f"Некорректный ввод: {e}")
            return

        movie = {
            "title": title,
            "genre": genre,
            "year": year,
            "rating": rating
        }

        self.movies.append(movie)
        self.update_movie_table()
        self.save_movies()

    def update_movie_table(self):
        for item in self.movie_table.get_children():
            self.movie_table.delete(item)
        for movie in self.movies:
            self.movie_table.insert("", "end", values=(movie["title"], movie["genre"], movie["year"], movie["rating"]))

    def filter_movies(self):
        genre_filter = self.filter_genre_entry.get().lower()
        filtered_movies = [movie for movie in self.movies if genre_filter in movie["genre"].lower()]
        self.movie_table.delete(*self.movie_table.get_children())
        for movie in filtered_movies:
            self.movie_table.insert("", "end", values=(movie["title"], movie["genre"], movie["year"], movie["rating"]))

    def load_movies(self):
        if os.path.exists("movies.json"):
            with open("movies.json", "r") as f:
                self.movies = json.load(f)
            self.update_movie_table()

    def save_movies(self):
        with open("movies.json", "w") as f:
            json.dump(self.movies, f)

if __name__ == "__main__":
    root = tk.Tk()
    app = MovieLibrary(root)
