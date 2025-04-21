## 뱀 게임 개발

### <main.py>
```python
from turtle import Screen
import time
from snake import Snake
from food import Food
from scoreboard import Scoreboard

screen = Screen()   # 화면 객체
screen.setup(width=600, height=600)   # 화면 사이즈
screen.bgcolor("black")   # 배경 화면 색
screen.title("My Snake Game")   # 화면 타이틀 이름
screen.tracer(0)   # 화면 애니메이션 끄기

snake = Snake()   # 뱀 객체
food = Food()   # 먹이 객체
scoreboard = Scoreboard()   # 점수판 객체

screen.listen()   # 화면 이벤트 모니터렁
screen.onkey(snake.up, "Up")   # up 버튼 이벤트 처리
screen.onkey(snake.down, "Down")   # down 버튼 이벤트 처리
screen.onkey(snake.left, "Left")   # left 버튼 이벤트 처리
screen.onkey(snake.right, "Right")   # right 버튼 이벤트 처리

game_is_on = True   # 게임 진행

while game_is_on:
    screen.update()   # 화면 갱신
    time.sleep(0.1)   # 0.1초 딜레이

    snake.move()   # 뱀 이동

    if snake.snake[0].distance(food) < 15:  # 뱀 머리와 먹이 사이 거리 비교
        food.refresh()   # 먹이 다시 그림
        scoreboard.score_up()   # 점수 증가
        snake.extend()   # 뱀 길이 확장

    # 뱀 머리가 벽을 넘어갈 경우
    if snake.snake[0].xcor() > 280 or snake.snake[0].xcor() < -280 or snake.snake[0].ycor() > 280 or snake.snake[0].ycor() < -280:
        game_is_on = False   # 게임 정지
        scoreboard.game_over()   # 게임 종료 표시

    # 뱀 머리가 몸통과 부딪힐 경우(
    for segment in snake.snake[1:]:   # 뱀 배열 중 뱀 머리는 제외
        if snake.snake[0].distance(segment) < 10:   # 뱀 머리와 몸통의 거리 비교
            game_is_on = False  # 게임 정지
            scoreboard.game_over()  # 게임 종료 표시


screen.exitonclick()   # 화면 홀딩(클릭할 때까지)
```

<br/><br/>
### <snake.py>
```python

from turtle import Turtle

START_POSITION = [(0, 0), (-20, 0), (-40, 0)]   # 뱀 최초 좌표(3개)
MOVE_FORWARD = 20   # 뱀 이동 속도


class Snake:

    def __init__(self):
        self.snake = []   # 뱀 배열
        self.create_snake()

    def create_snake(self):   # 뱀 최초 생성
        for position in START_POSITION:
            self.add_square(position)   # 뱀 배열에 추가

    def add_square(self, position):
        square = Turtle(shape="square")  # 사각형 객채
        square.penup()
        square.color("white")
        square.goto(position)
        self.snake.append(square)  # 객체를 뱀 배열에 추가

    def extend(self):
        self.add_square(self.snake[-1].position())   # 뱀 배열 마지막 위치에 추가

    def move(self):   # 뱀 이동
        for segment in range(len(self.snake) - 1, 0, -1):  # 뱀 몸통 뒤부터 앞으로 하나씩 이동
            new_x = self.snake[segment-1].xcor()
            new_y = self.snake[segment-1].ycor()
            self.snake[segment].goto(new_x, new_y)   # 뱀 앞 몸통 좌표로 하나씩 덮어씀
        self.snake[0].forward(MOVE_FORWARD)   # 뱀 머리 이동

    def up(self):   # 위로 이동
        if self.snake[0].heading() != 270:   # 반대 방향이 아닐 경우만
            self.snake[0].setheading(90)

    def down(self):   # 아래로 이동
        if self.snake[0].heading() != 90:   # 반대 방향이 아닐 경우만
            self.snake[0].setheading(270)

    def left(self):   # 왼쪽으로 이동
        if self.snake[0].heading() != 0:   # 반대 방향이 아닐 경우만
            self.snake[0].setheading(180)

    def right(self):   # 오른쪽으로 이동
        if self.snake[0].heading() != 180:   # 반대 방향이 아닐 경우만
            self.snake[0].setheading(0)
```

<br/><br/>
### <food.py>
```python
from turtle import Turtle
import random


class Food(Turtle):   # Turtle 클래스 상속
    def __init__(self):
        super().__init__()
        self.shape("circle")   # 먹이 모양
        self.penup()   # 선 없이 이동
        self.shapesize(stretch_len=0.5, stretch_wid=0.5)   # 먹이 사이즈(10x10)
        self.color("blue")   # 먹이 색
        self.speed("fastest")   # 먹이 이동 속도
        self.refresh()   # 먹이 재생성

    def refresh(self):   # 화면 범위 내에서 먹이 랜덤 생성
        random_x = random.randint(-280, 280)
        random_y = random.randint(-280, 280)
        self.goto(random_x, random_y)   # 먹이 좌표 이동
```

<br/><br/>
### <scoreboard.py>
```python
from turtle import Turtle
ALIGNMENT = "center"
FONT = ("Courier", 18, "normal")

class Scoreboard(Turtle):   # Tultle 클래스 상속
    def __init__(self):
        super().__init__()
        self.score = 0   # 점수 초기화
        self.color("white")   # 점수판 색
        self.penup()   # 선 없이 이동
        self.goto(0, 270)   # 점수판 위치
        self.update_score()   # 점수판 표시
        self.hideturtle()   # 터틀(화살표) 숨김

    def update_score(self):
        self.write(f"Score: {self.score}", align=ALIGNMENT, font=FONT)  # 점수 표시

    def game_over(self):
        self.goto(0, 0)   # 종료 문자 위치
        self.write("Game Over!", align=ALIGNMENT, font=FONT)  # 게임 종료 표시

    def score_up(self):   # 먹이를 먹으면 점수 증가
        self.clear()   # 점수판 클리어
        self.score += 1   # 점수 증가
        self.update_score()   # 점수판 표시
```
