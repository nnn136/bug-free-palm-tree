ФИО: Попова Марина Александровна


import tkinter as tk
from tkinter import messagebox, ttk
import json

class BookTracker:
    def __init__(self, master):
        self.master = master
        master.title("Book Tracker")

        self.title_label = tk.Label(master, text="Название книги:")
        self.title_label.pack()
        
        self.title_entry = tk.Entry(master)
        self.title_entry.pack()

        self.author_label = tk.Label(master, text="Автор:")
        self.author_label.pack()

        self.author_entry = tk.Entry(master)
        self.author_entry.pack()

        self.genre_label = tk.Label(master, text="Жанр:")
        self.genre_label.pack()

        self.genre_entry = tk.Entry(master)
        self.genre_entry.pack()
        
        self.pages_label = tk.Label(master, text="Количество страниц:")
        self.pages_label.pack()

        self.pages_entry = tk.Entry(master)
        self.pages_entry.pack()

        self.add_button = tk.Button(master, text="Добавить книгу", command=self.add_book)
        self.add_button.pack()

        self.filter_label = tk.Label(master, text="Фильтр по жанру:")
        self.filter_label.pack()

        self.filter_entry = tk.Entry(master)
        self.filter_entry.pack()

        self.filter_button = tk.Button(master, text="Фильтровать", command=self.filter_books)
        self.filter_button.pack()
        
        self.books_list = ttk.Treeview(master, columns=("title", "author", "genre", "pages"), show='headings')
        self.books_list.heading("title", text="Название книги")
        self.books_list.heading("author", text="Автор")
        self.books_list.heading("genre", text="Жанр")
        self.books_list.heading("pages", text="Количество страниц")
        self.books_list.pack()

        self.books = []
        self.load_books()

    def add_book(self):
        title = self.title_entry.get()
        author = self.author_entry.get()
        genre = self.genre_entry.get()
        pages_str = self.pages_entry.get()
        
        if not title or not author or not genre or not pages_str:
            messagebox.showerror("Ошибка", "Все поля должны быть заполнены.")
            return
        
        try:
            pages = int(pages_str)
        except ValueError:
            messagebox.showerror("Ошибка", "Количество страниц должно быть числом.")
            return

        book = {
            "title": title,
            "author": author,
            "genre": genre,
            "pages": pages
        }
        self.books.append(book)
        self.books_list.insert("", tk.END, values=(title, author, genre, pages))
        self.save_books()

    def filter_books(self):
        genre_filter = self.filter_entry.get().lower()
        for item in self.books_list.get_children():
            self.books_list.delete(item)
        
        for book in self.books:
            if genre_filter in book["genre"].lower():
                self.books_list.insert("", tk.END, values=(book["title"], book["author"], book["genre"], book["pages"]))
    
    def load_books(self):
        try:
            with open("books.json", "r") as f:
                self.books = json.load(f)
                for book in self.books:
                    self.books_list.insert("", tk.END, values=(book["title"], book["author"], book["genre"], book["pages"]))
        except FileNotFoundError:
            self.books = []

    def save_books(self):
        with open("books.json", "w") as f:
            json.dump(self.books, f)

if __name__ == "__main__":
    root = tk.Tk()
    app = BookTracker(root)
    root.mainloop()

Описание программы:
Программа "Book Tracker" предназначена для управления списком прочитанных книг. 
С её помощью пользователи могут добавлять информацию о книгах, включая название, автора, жанр и количество страниц. 
Также реализована возможность фильтрации по жанру и количеству страниц. 
Все данные хранятся в формате JSON, что позволяет их легко сохранять и загружать. 
Приложение имеет интуитивно понятный графический интерфейс, который упрощает взаимодействие с пользователем.

Шаг 1: Создание интерфейса
Для создания графического интерфейса можно использовать библиотеку Tkinter. 
Шаг 2: Добавление книги
Кнопка "Добавить книгу" реализована в методе add_book(), который проверяет вводимые данные и добавляет новую книгу в список и таблицу.

Шаг 3: Фильтрация
Функция для фильтрации по жанру реализована в методе filter_books(), который очищает список книг и отображает только те, которые соответствуют введенному жанру.

Шаг 4: Сохранение и загрузка в JSON