![image](https://github.com/user-attachments/assets/2f4e78cc-8b9c-4752-82b4-5814e3342bfd)提交截图，加上自己的姓名
import tkinter as tk
import random

# 游戏配置
CELL_SIZE = 20  # 每个格子的大小
GRID_WIDTH = 20  # 水平方向的格子数量
GRID_HEIGHT = 20  # 垂直方向的格子数量
DELAY = 100  # 刷新间隔（毫秒）

# 方向
UP = (0, -1)
DOWN = (0, 1)
LEFT = (-1, 0)
RIGHT = (1, 0)

class SnakeGame(tk.Tk):
    def __init__(self):
        super().__init__()
        self.title("贪吃蛇")
        self.geometry(f"{GRID_WIDTH * CELL_SIZE}x{GRID_HEIGHT * CELL_SIZE + 40}")  # 加高窗口高度以放置计分板
        self.resizable(False, False)

        # 画布
        self.canvas = tk.Canvas(self, width=GRID_WIDTH * CELL_SIZE, height=GRID_HEIGHT * CELL_SIZE, bg="black")
        self.canvas.pack()

        # 计分板
        self.score_label = tk.Label(self, text="得分: 0", font=("Arial", 12))
        self.score_label.pack()

        # 初始化游戏状态
        self.snake = [(5, 5), (5, 4), (5, 3)]  # 蛇的初始位置
        self.food = self.create_food()  # 创建食物
        self.direction = RIGHT  # 初始方向
        self.game_over = False
        self.score = 0
        self.new_snake_part = False  # 记录蛇是否吃到食物，是否需要增加长度

        # 绑定键盘事件
        self.bind("<KeyPress>", self.on_key_press)

        # 游戏循环
        self.update_game()

    def create_food(self):
        """随机生成食物，确保食物不在蛇的身体上"""
        while True:
            food = (random.randint(0, GRID_WIDTH - 1), random.randint(0, GRID_HEIGHT - 1))
            if food not in self.snake:
                return food

    def on_key_press(self, event):
        """处理按键事件来改变蛇的方向"""
        key = event.keysym
        if key == "Up" and self.direction != DOWN:
            self.direction = UP
        elif key == "Down" and self.direction != UP:
            self.direction = DOWN
        elif key == "Left" and self.direction != RIGHT:
            self.direction = LEFT
        elif key == "Right" and self.direction != LEFT:
            self.direction = RIGHT

    def update_game(self):
        """游戏主循环"""
        if not self.game_over:
            self.move_snake()
            self.check_collision()
            self.draw_elements()
            self.after(DELAY, self.update_game)
        else:
            self.canvas.create_text(GRID_WIDTH * CELL_SIZE // 2, GRID_HEIGHT * CELL_SIZE // 2,
                                    text="GAME OVER", fill="red", font=("Arial", 24))

    def move_snake(self):
        """根据当前方向移动蛇"""
        head_x, head_y = self.snake[0]
        new_head = (head_x + self.direction[0], head_y + self.direction[1])

        # 检查是否吃到食物
        if new_head == self.food:
            self.food = self.create_food()  # 重新生成食物
            self.score +=1
            self.new_snake_part = True  # 吃到食物时记录需要增加蛇身
        else:
            self.new_snake_part = False

        # 显示得分
        self.score_label.config(text=f"得分: {self.score}")

        # 将新头插入到蛇头
        self.snake.insert(0, new_head)

        if not self.new_snake_part:
            self.snake.pop()

    def check_collision(self):
        """检测蛇是否撞到墙或自身"""
        head_x, head_y = self.snake[0]
        # 检查是否撞墙
        if head_x < 0 or head_x >= GRID_WIDTH or head_y < 0 or head_y >= GRID_HEIGHT:
            self.game_over = True
        # 检查是否撞到自己
        if len(self.snake) != len(set(self.snake)):
            self.game_over = True

    def draw_elements(self):
        """绘制蛇和食物"""
        self.canvas.delete(tk.ALL)  # 清空画布
        # 画蛇
        for x, y in self.snake:
            self.canvas.create_rectangle(x * CELL_SIZE, y * CELL_SIZE,
                                         (x + 1) * CELL_SIZE, (y + 1) * CELL_SIZE,
                                         fill="green")
        # 画食物
        food_x, food_y = self.food
        self.canvas.create_oval(food_x * CELL_SIZE, food_y * CELL_SIZE,
                                (food_x + 1) * CELL_SIZE, (food_y + 1) * CELL_SIZE,
                                fill="red")

# 启动游戏
if __name__ == "__main__":
    game = SnakeGame()
    game.mainloop()
![image](https://github.com/user-attachments/assets/2c34620c-a27c-410f-bd41-eaba33f59d4c)

