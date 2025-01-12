import tkinter as tk
import random

# Oyun penceresini oluşturma
window = tk.Tk()
window.title("Yılan Oyunu")
window.resizable(False, False)
canvas = tk.Canvas(window, width=600, height=400, bg="black")
canvas.pack()

# Başlangıç değerleri
snake = [(20, 20), (30, 20), (40, 20)]  # Yılanın başlangıç segmentleri
snake_direction = "Right"  # Başlangıç yönü
food_position = (100, 100)  # Başlangıçta yemin konumu
score = 0  # Başlangıç puanı

# Yılanı çizme fonksiyonu
def draw_snake():
    canvas.delete("snake")
    for x, y in snake:
        canvas.create_rectangle(x, y, x + 20, y + 20, fill="green", tag="snake")

# Yemi çizme fonksiyonu
def draw_food():
    canvas.delete("food")
    x, y = food_position
    canvas.create_rectangle(x, y, x + 20, y + 20, fill="red", tag="food")

# Yılanın hareketi
def move_snake():
    global snake, food_position, score

    head_x, head_y = snake[-1]

    if snake_direction == "Right":
        head_x += 20
    elif snake_direction == "Left":
        head_x -= 20
    elif snake_direction == "Up":
        head_y -= 20
    elif snake_direction == "Down":
        head_y += 20

    new_head = (head_x, head_y)

    # Duvara çarpma veya kendine çarpma kontrolü
    if new_head in snake or head_x < 0 or head_y < 0 or head_x >= 600 or head_y >= 400:
        game_over()
        return

    snake.append(new_head)

    # Yem yeme kontrolü
    if new_head == food_position:
        score += 10
        food_position = (random.randint(0, 29) * 20, random.randint(0, 19) * 20)
    else:
        snake.pop(0)

    draw_snake()
    draw_food()
    window.after(100, move_snake)

# Yön değiştirme fonksiyonu
def change_direction(new_direction):
    global snake_direction
    if new_direction == "Left" and snake_direction != "Right":
        snake_direction = "Left"
    elif new_direction == "Right" and snake_direction != "Left":
        snake_direction = "Right"
    elif new_direction == "Up" and snake_direction != "Down":
        snake_direction = "Up"
    elif new_direction == "Down" and snake_direction != "Up":
        snake_direction = "Down"

# Yön tuşlarını bağlama
window.bind("<Left>", lambda event: change_direction("Left"))
window.bind("<Right>", lambda event: change_direction("Right"))
window.bind("<Up>", lambda event: change_direction("Up"))
window.bind("<Down>", lambda event: change_direction("Down"))

# Oyun bittiğinde yapılacaklar
def game_over():
    canvas.create_text(300, 200, text="Oyun Bitti!", fill="white", font=("Arial", 24))
    window.after_cancel(move_snake)  # Hareketi durdur

# Oyunu başlatma
draw_snake()
draw_food()
move_snake()
window.mainloop()
