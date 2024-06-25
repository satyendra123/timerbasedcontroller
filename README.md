# timerbasedcontroller
timer based controller for boom barrier


# i want to make the menus and submenus based system using python
from machine import Pin
import time
import json

# Define GPIO pins for the buttons
button_menu = Pin(2, Pin.IN, Pin.PULL_UP)
button_up = Pin(0, Pin.IN, Pin.PULL_UP)
button_down = Pin(4, Pin.IN, Pin.PULL_UP)
button_ok = Pin(5, Pin.IN, Pin.PULL_UP)
button_exit = Pin(26, Pin.IN, Pin.PULL_UP)

# Define the menu and submenu structure
menu = ["F1", "F2", "F3", "F4", "F5", "F6"]

submenu = {
    "F1": {"1": {"name": "option1", "timer": 0}, "2": {"name": "option2", "timer": 0}, "3": {"name": "option3", "timer": 0}, "4": {"name": "option4", "timer": 0}, "5": {"name": "option5", "timer": 0}},
    "F2": {"1": {"name": "option1", "timer": 0}, "2": {"name": "option2", "timer": 0}, "3": {"name": "option3", "timer": 0}, "4": {"name": "option4", "timer": 0}, "5": {"name": "option5", "timer": 0}},
    "F3": {"1": {"name": "option1", "timer": 0}, "2": {"name": "option2", "timer": 0}, "3": {"name": "option3", "timer": 0}, "4": {"name": "option4", "timer": 0}, "5": {"name": "option5", "timer": 0}},
    "F4": {"1": {"name": "option1", "timer": 0}, "2": {"name": "option2", "timer": 0}, "3": {"name": "option3", "timer": 0}, "4": {"name": "option4", "timer": 0}, "5": {"name": "option5", "timer": 0}},
    "F5": {"1": {"name": "option1", "timer": 0}, "2": {"name": "option2", "timer": 0}, "3": {"name": "option3", "timer": 0}, "4": {"name": "option4", "timer": 0}, "5": {"name": "option5", "timer": 0}},
}

current_menu_index = 0
current_submenu_index = 1
in_submenu = False
setting_timer = False

def save_timers():
    with open('timers.txt', 'w') as f:
        json.dump(submenu, f)

def load_timers():
    global submenu
    try:
        with open('timers.txt', 'r') as f:
            submenu = json.load(f)
    except OSError:
        save_timers()  # Create the file if it doesn't exist

def print_current_menu(index):
    print(f"Current Menu: {menu[index]}")

def print_current_submenu(menu_name, index):
    submenu_items = list(submenu[menu_name].items())
    print(f"Submenu for {menu_name}: {submenu_items[index - 1][0]}: {submenu_items[index - 1][1]['name']} (Timer: {submenu_items[index - 1][1]['timer']} sec)")

def button_pressed(pin):
    return not pin.value()

load_timers()
print_current_menu(current_menu_index)

while True:
    if button_pressed(button_exit):
        if setting_timer:
            setting_timer = False
            print_current_submenu(menu[current_menu_index], current_submenu_index)
        elif in_submenu:
            in_submenu = False
            print_current_menu(current_menu_index)
        else:
            print_current_menu(current_menu_index)
        time.sleep(0.3)  # Debounce delay

    elif button_pressed(button_up):
        if setting_timer:
            submenu[menu[current_menu_index]][str(current_submenu_index)]['timer'] += 1
            print(f"Set Timer: {submenu[menu[current_menu_index]][str(current_submenu_index)]['timer']} sec")
        elif in_submenu:
            current_submenu_index = (current_submenu_index % len(submenu[menu[current_menu_index]])) + 1
            print_current_submenu(menu[current_menu_index], current_submenu_index)
        else:
            current_menu_index = (current_menu_index + 1) % len(menu)
            print_current_menu(current_menu_index)
        time.sleep(0.3)  # Debounce delay

    elif button_pressed(button_down):
        if setting_timer:
            submenu[menu[current_menu_index]][str(current_submenu_index)]['timer'] = max(0, submenu[menu[current_menu_index]][str(current_submenu_index)]['timer'] - 1)
            print(f"Set Timer: {submenu[menu[current_menu_index]][str(current_submenu_index)]['timer']} sec")
        elif in_submenu:
            current_submenu_index = (current_submenu_index - 2) % len(submenu[menu[current_menu_index]]) + 1
            print_current_submenu(menu[current_menu_index], current_submenu_index)
        else:
            current_menu_index = (current_menu_index - 1) % len(menu)
            print_current_menu(current_menu_index)
        time.sleep(0.3)  # Debounce delay

    elif button_pressed(button_ok):
        if in_submenu and not setting_timer:
            setting_timer = True
            print(f"Set Timer: {submenu[menu[current_menu_index]][str(current_submenu_index)]['timer']} sec")
        elif not in_submenu:
            in_submenu = True
            current_submenu_index = 1
            print_current_submenu(menu[current_menu_index], current_submenu_index)
        save_timers()
        time.sleep(0.3)  # Debounce delay

