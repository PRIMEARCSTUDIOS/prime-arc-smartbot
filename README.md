"""

Modes: 0 = idle, 1 = compass, 2 = countdown

"""
# Show one of eight compass directions.
def showCompass():
    global heading, sector, lastDirection
    heading = input.compass_heading()
    if heading >= 0:
        sector = Math.floor((heading + 22.5) / 45) % 8
        if sector != lastDirection:
            lastDirection = sector
            if sector == 0:
                basic.show_string("N")
            elif sector == 1:
                basic.show_string("NE")
            elif sector == 2:
                basic.show_string("E")
            elif sector == 3:
                basic.show_string("SE")
            elif sector == 4:
                basic.show_string("S")
            elif sector == 5:
                basic.show_string("SW")
            elif sector == 6:
                basic.show_string("W")
            else:
                basic.show_string("NW")
# Rock: all 25 LEDs on.
def showRock():
    led.plot_all()
# Button A: continuous compass mode.

def on_button_pressed_a():
    global mode, lastDirection
    mode = 1
    lastDirection = -1
    input.compass_heading()
input.on_button_pressed(Button.A, on_button_pressed_a)

# Paper: four corner LEDs.
def showPaper():
    basic.clear_screen()
    led.plot(0, 0)
    led.plot(4, 0)
    led.plot(0, 4)
    led.plot(4, 4)
# A+B: creator credit.

def on_button_pressed_ab():
    global mode
    mode = 0
    basic.show_string("CREATED BY SHRIHAN TRIPATHI")
input.on_button_pressed(Button.AB, on_button_pressed_ab)

# Button B: 10-second countdown.

def on_button_pressed_b():
    global mode
    mode = 2
    
    def on_in_background():
        global mode
        # Show 10, then static single-digit numbers.
        basic.show_number(10)
        n = 9
        while n >= 0:
            if mode != 2:
                return
            showDigit(n)
            basic.pause(1000)
            n -= 1
        if mode == 2:
            basic.show_icon(IconNames.YES)
            mode = 0
    control.in_background(on_in_background)
    
input.on_button_pressed(Button.B, on_button_pressed_b)

# Shake: first introduce the game, then choose randomly.

def on_gesture_shake():
    global mode, gameReady, choice
    mode = 0
    if not (gameReady):
        basic.show_string("ROCK")
        basic.show_string("PAPER")
        basic.show_string("SCISSORS")
        basic.show_icon(IconNames.HAPPY)
        gameReady = True
    else:
        choice = Math.random_range(0, 2)
        if choice == 0:
            showRock()
        elif choice == 1:
            showPaper()
        else:
            showScissors()
input.on_gesture(Gesture.SHAKE, on_gesture_shake)

# Scissors: X pattern.
def showScissors():
    basic.clear_screen()
    for i in range(5):
        led.plot(i, i)
        led.plot(4 - i, i)
# Draw a digit directly on the 5x5 LED display.
# These patterns do not scroll.
def showDigit(o: number):
    global patterns, rows
    patterns = [["01110",
            "10001",
            "10011",
            "10101",
            "11001",
            "10001",
            "01110"],
        ["00100",
            "01100",
            "00100",
            "00100",
            "00100",
            "00100",
            "01110"],
        ["01110",
            "10001",
            "00001",
            "00010",
            "00100",
            "01000",
            "11111"],
        ["11110",
            "00001",
            "00001",
            "01110",
            "00001",
            "00001",
            "11110"],
        ["00010",
            "00110",
            "01010",
            "10010",
            "11111",
            "00010",
            "00010"],
        ["11111",
            "10000",
            "10000",
            "11110",
            "00001",
            "00001",
            "11110"],
        ["01110",
            "10000",
            "10000",
            "11110",
            "10001",
            "10001",
            "01110"],
        ["11111",
            "00001",
            "00010",
            "00100",
            "01000",
            "01000",
            "01000"],
        ["01110",
            "10001",
            "10001",
            "01110",
            "10001",
            "10001",
            "01110"],
        ["01110",
            "10001",
            "10001",
            "01111",
            "00001",
            "00001",
            "01110"]]
    basic.clear_screen()
    # Compress a 7-row digit into the 5-row display.
    # Use rows 0, 1, 3, 5, 6 for a compact recognizable digit.
    rows = [0, 1, 3, 5, 6]
    for y in range(5):
        for x in range(5):
            if patterns[o][rows[y]].char_at(x) == "1":
                led.plot(x, y)
rows: List[number] = []
patterns: List[List[str]] = []
choice = 0
gameReady = False
sector = 0
heading = 0
lastDirection = 0
mode = 0
lastDirection = -1
# Continuously update compass while compass mode is active.

def on_forever():
    if mode == 1:
        showCompass()
        basic.pause(100)
    else:
        basic.pause(50)
basic.forever(on_forever)
