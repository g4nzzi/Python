## Pomodoro(일/휴식 타이머) 개발

### <main.py>

```python
from tkinter import *
import math

# ---------------------------- CONSTANTS ------------------------------- #
PINK = "#e2979c"
RED = "#e7305b"
GREEN = "#9bdeac"
YELLOW = "#f7f5dd"
FONT_NAME = "Courier"   # 글꼴
WORK_MIN = 25   # 일하는 시간
SHORT_BREAK_MIN = 5   # 짧은 휴식 시간
LONG_BREAK_MIN = 20   # 긴 휴식
resp = 0   # 반복 횟수
mark = ""   # 체크 표시 저장
timer = None   # 시간 타이머

# ---------------------------- TIMER RESET ------------------------------- # 
def reset_timer():
    global resp
    resp = 0   # 반복 회수 초기화
    window.after_cancel(timer)   # 시간 타이머 초기화
    main_label.config(text="Timer", fg=GREEN)   # 메인 래이블 초기화
    canvas.itemconfig(timer_text, text="00:00")   # 시간 캔버스 초기화
    check_mark.config(text="")   # 체크 표시 초기화

# ---------------------------- TIMER MECHANISM ------------------------------- # 
def start_timer():
    global resp, mark
    work_time = WORK_MIN * 60   # 일하는 시간(초)
    s_break_time = SHORT_BREAK_MIN * 60   # 짧은 휴식 시간(초)
    l_break_time = LONG_BREAK_MIN * 60   # 긴 휴식 시간(초)
    resp += 1   # 반복 횟수 증가

    if resp % 2 == 0:   # 짝수 반복 시
        countdown(s_break_time)   # 짧은 휴식 호출
        main_label.config(text="Break", fg=PINK)   # 메인 래이블 변경
        mark += "✔"   # 체크 표시 추가
        check_mark.config(text=mark)   # 체크 표시 반영
    elif resp % 8 == 0:   # 여덟번째 반복 시
        countdown(l_break_time)   # 긴 휴식 호출
        main_label.config(text="Break", fg=RED)   # 메인 래이블 변경
        mark += "✔"   # 체크 표시 추가
        check_mark.config(text=mark)   # 체크 표시 반영
    else:
        countdown(work_time)   # 일하는 시간 호출
        main_label.config(text="Work", fg=GREEN)   # 메인 래이블 변경

# ---------------------------- COUNTDOWN MECHANISM ------------------------------- # 
def countdown(time):   # 시간 카운트

    time_min = math.floor(time / 60)   # 분 저장(소수점 이하 버림)
    time_sec = time % 60   # 초 저장
    if time_sec < 10:   # 10초 미만의 경우
        time_sec = f"0{time_sec}"   # 앞에 "0" 붙여서 두자리 표시

    canvas.itemconfig(timer_text, text=f"{time_min}:{time_sec}")   # 캔버스 텍스트 표시 형태 재정의
    if time > 0:   # 시간이 0초 될 때까지
        global timer
        timer = window.after(1000, countdown, time - 1)   # 1초 단위 반복 타이머(재귀호출)
    else:
        start_timer()   # 시간이 0초 되면 다시 start 호출

# ---------------------------- UI SETUP ------------------------------- #

window = Tk()   # Tkinter 객체
window.title("Pomodoro")
window.config(padx=100, pady=50, bg=YELLOW)   # Tkinter 윈도우 창

main_label = Label(text="Timer", font=(FONT_NAME, 50, "bold"), fg=GREEN, bg=YELLOW)
main_label.grid(row=0, column=1)   # 메인 래이블 그리기

canvas = Canvas(width=200, height=224, bg=YELLOW, highlightthickness=0)   # 배경 캔버스
tomato_img = PhotoImage(file="tomato.png")   # 이미지 로드
canvas.create_image(100, 112, image=tomato_img)   # 캔버스에 이미지 삽입
timer_text = canvas.create_text(100, 130, text="00:00", fill="white", font=(FONT_NAME, 28, "bold"))   # 캔버스 텍스트
canvas.grid(row=1, column=1)   # 캔버스 그리기

start_button = Button(text="Start", highlightthickness=0, command=start_timer)   # start 버튼
start_button.grid(row=2, column=0)   # start 버튼 그리기
reset_button = Button(text="Reset", highlightthickness=0, command=reset_timer)   # reset 버튼
reset_button.grid(row=2, column=2)   # reset 버튼 그리기

check_mark = Label(fg=GREEN, bg=YELLOW)   # 체크 표시 래이블
check_mark.grid(row=3, column=1)   # 체크 표시 래이블 그리기


window.mainloop()   # Tkinter 윈도우 창 지속 띄움
```
