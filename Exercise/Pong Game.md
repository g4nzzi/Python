## 퐁(벽돌깨기) 게임 개발

### <main.py>

```python
from turtle import Screen
from paddle import Paddle
from ball import Ball
from scoreboard import Scoreboard
import time

screen = Screen()   # 화면 객체
screen.setup(width=800, height=600)   # 화면 사이즈
screen.bgcolor("black")   # 배경 화면 색
screen.title("Pong Game")   # 화면 타이틀 이름
screen.tracer(0)   # 화면 애니메이션 끄기

r_paddle = Paddle((350, 0))   # 우측 패들 초기 위치
l_paddle = Paddle((-350, 0))   # 좌측 패들 초기 위치

ball = Ball()   # 공 객체
scoreboard = Scoreboard()   # 점수판 객체

screen.listen()   # 키 입력 대기
screen.onkey(r_paddle.go_up, "Up")   # up 키 이벤트 처리
screen.onkey(r_paddle.go_down, "Down")   # down 키 이벤트 처리
screen.onkey(l_paddle.go_up, "w")   # w 키 이벤트 처리
screen.onkey(l_paddle.go_down, "s")   # s 키 이벤트 처리

game_is_on = True   # 게임 진행 상태값

while game_is_on:   # 게임 진행(반복)
    time.sleep(ball.move_speed)   # 화면 업데이트 지연
    screen.update()   # 화면 업데이트
    ball.move()   # 공 이동

    if ball.ycor() > 280 or ball.ycor() < -280:   # 공이 상,하 벽에 닿을 경우
        ball.bounce_y()   # 상,하 반전

    # 공이 좌,우 패들과 50 이하의 거리이면서 x값이 320(패들 위치) 이상일 경우
    if ball.distance(r_paddle) < 50 and ball.xcor() > 320 or ball.distance(l_paddle) < 50 and ball.xcor() < -320:
        ball.bounce_x()   # 좌,우 반전

    if ball.xcor() > 380:   # 공이 우측 벽면에 닿을 경우
        ball.reset_position()   # 공 초기화
        scoreboard.l_point()   # 좌측 점수 증가

    if ball.xcor() < -380:   # 공이 좌측 벽면에 닿을 경우
        ball.reset_position()   # 공 초기화
        scoreboard.r_point()   # 우측 점수 증가


screen.exitonclick()   # 화면 홀딩(클릭할 때까지)

```

<br/><br/>
### <paddle.py>

```python
from turtle import Turtle

class Paddle(Turtle):

    def __init__(self, position):   # 패들 초기화
        super().__init__()
        self.color("white")
        self.shape("square")
        self.shapesize(stretch_wid=5, stretch_len=1)   # 패들 크기
        self.penup()
        self.goto(position)

    def go_up(self):   # 패등 위로 이동 처리
        new_y = self.ycor() + 30   # 위로 30 이동
        self.goto(self.xcor(), new_y)

    def go_down(self):   # 패등 아래로 이동 처리
        new_y = self.ycor() - 30   # 아래로 30 이동
        self.goto(self.xcor(), new_y)
```

<br/><br/>
### <ball.py>

```python
from turtle import Turtle

class Ball(Turtle):

    def __init__(self):   # 공 초기화
        super().__init__()
        self.color("white")
        self.shape("circle")
        self.penup()
        self.x_move = 10   # 공 좌,우 이동값
        self.y_move = 10   # 공 상,하 이동값
        self.move_speed = 0.1   # 공 초기 이동 스피드

    def move(self):   # 공 이동
        new_x = self.xcor() + self.x_move   # 좌,우 이동
        new_y = self.ycor() + self.y_move   # 상,하 이동
        self.goto(new_x, new_y)

    def bounce_y(self):   # 상,하 반전
        self.y_move *= -1   # 상,하 벽에 닿을 경우 y값 반전

    def bounce_x(self):   # 좌,우 반전
        self.x_move *= -1   # 좌,우 패들에 닿을 경우 x값 반전
        self.move_speed *= 0.9   # 공 이동 스피트 0.9 증가

    def reset_position(self):   # 공 초기화
        self.goto(0, 0)   # 공 위치 초기화
        self.move_speed = 0.1   # 공 이동 스피드 초기화
        self.bounce_x()   # 좌,우 반전
```

<br/><br/>
### <scoreboard.py>

```python
from turtle import Turtle

class Scoreboard(Turtle):

    def __init__(self):   # 점수판 초기화
        super().__init__()
        self.color("white")
        self.penup()
        self.hideturtle()
        self.l_score = 0   # 좌측 초기 점수
        self.r_score = 0   # 우측 초기 점수
        self.score_update()   # 점수판 업데이트

    def score_update(self):   # 점수판 업데이트
        self.clear()
        self.goto(-100, 200)
        self.write(self.l_score, align="center", font=("Courier", 80, "normal"))
        self.goto(100, 200)
        self.write(self.r_score, align="center", font=("Courier", 80, "normal"))

    def l_point(self):
        self.l_score += 1   # 좌측 점수 증가
        self.score_update()   # 점수판 업데이트

    def r_point(self):
        self.r_score += 1   # 우측 점수 증가
        self.score_update()   # 점수판 업데이트
```
