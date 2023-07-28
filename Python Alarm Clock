from tkinter import *
from tkinter import ttk
from tktimepicker import SpinTimePickerModern, constants
from tkcalendar import *
from PIL import ImageTk, Image
from datetime import datetime as d
import datetime
import webbrowser
import winsound
import json
import uuid
import time
import sys
import os


class AlarmApp:
    def resource_path(self, relative_path):
        """ Get absolute path to resource, works for dev and for PyInstaller """
        try:
            # PyInstaller creates a temp folder and stores path in _MEIPASS
            base_path = sys._MEIPASS
        except Exception:
            base_path = os.path.abspath(".")

        return os.path.join(base_path, relative_path)

    def __init__(self, master):
        self.master = master

        master.title('My Alarm Clock')
        master.iconbitmap(self.resource_path("alarm_clock.ico"))
        master.geometry('450x200')
        master.resizable(0, 0)
        self.canvas = Canvas(master, bg='#808080').pack(fill='both')

        # clock and calendar labels:
        self.home_clock_lbl = Label(master, bg='#808080', fg='white', font=('Arial', 35))
        self.home_clock_lbl.place(x=90, y=45)
        self.home_cal_lbl = Label(master, bg='#808080', fg='white', font=('Arial', 15))
        self.home_cal_lbl.place(x=174, y=100)

        # time picker:
        self.time_picker = SpinTimePickerModern(master)
        self.time_picker.addAll(constants.HOURS12)  # adds hours clock, minutes and period
        self.time_picker.configureAll(bg="#404040", height=1, fg='white', hoverbg="#404040",
                                      font=15, hovercolor="#d73333", clickedbg="#2e2d2d", clickedcolor="#d73333")
        self.time_picker.configure_seprator(bg="#404040", fg="#ffffff")

        # calendar picker:
        self.calendar = Calendar(master, selectmode='day', date_pattern='d/m/y', mindate=d.now())

        # logo files:
        self.github_logo_file = Image.open(self.resource_path("githublogo.png"))
        self.github_link_logo = ImageTk.PhotoImage(self.github_logo_file)
        self.youtube_logo_file = Image.open(self.resource_path("youtubelogo.png"))
        self.youtube_link_logo = ImageTk.PhotoImage(self.youtube_logo_file)

        # other labels:
        self.instruction_lbl = Label(master, text='Hover and scroll to select', bg='#808080', fg='white')
        self.info_msg = Label(master, bg='#808080', fg='white')
        self.info_msg.place()
        self.creator_lbl = ttk.Label(master, background='#808080', foreground='white',
                                     text="My Alarm Clock\nVersion 1.50\nDeveloped by: Tauhid Ahmed", font=('Arial', 11))
        self.dev_git_label = Label(master, cursor='hand1', image=self.github_link_logo)
        self.dev_youtube_label = Label(master, cursor='hand1', image=self.youtube_link_logo)
        self.remaining_lbl = Label(master, bg='#808080', fg='white', font=('Arial', 8))

        # alarm box and scrollbar:
        self.alarm_frame = LabelFrame(master, text='Alarms:', bg='#808080', fg='white')
        self.alarm_box = Listbox(self.alarm_frame)
        self.y_scrollbar = ttk.Scrollbar(self.alarm_box)
        self.alarm_box.pack(fill='both', expand='yes')
        self.alarm_box.bind("<<ListboxSelect>>", self.delete_alarm)
        self.y_scrollbar.pack(side=RIGHT, fill='y')
        self.alarm_box.configure( bg='#808080', fg='white', cursor='x_cursor', yscrollcommand=self.y_scrollbar.set)
        self.y_scrollbar.configure(command=self.alarm_box.yview)

        # BUTTONS:
        # set alarm menu button:
        self.set_menu_btn = ttk.Button(master, text='Set Alarm', command=self.set_alarm_screen, width=9)
        self.set_menu_btn.place(x=10, y=10)
        # add btn
        self.add_btn = ttk.Button(master, text='Add', command=self.add, width=4)
        # done btn:
        self.done_btn = ttk.Button(master, text='Done', command=self.exit_to_home_menu, width=5)
        # about btn:
        self.about_btn = ttk.Button(master, text='About', command=self.about, width=6)
        self.about_btn.place(x=395, y=10)
        self.back_btn = ttk.Button(command=self.exit_to_home_menu, text='Back', width=5)
        self.alarm_stop_btn = ttk.Button(root, text='Stop', width=5, command=self.stop_alarm)

        self.sys_timestamp = int(time.time())
        self.start_count = 0
        self.alarms = []
        self.executed_alarm_id = ''
        self.next_to_count = []

    def about(self):
        # widgets to remove:
        self.alarm_frame.place_forget()
        self.home_cal_lbl.place_forget()
        self.home_clock_lbl.place_forget()
        self.about_btn.place_forget()
        self.set_menu_btn.place_forget()
        # widgets to create:
        self.back_btn.place(x=10, y=10)
        self.creator_lbl.place(x=125, y=50)
        self.dev_git_label.place(x=185, y=115)
        self.dev_git_label.bind('<Button>', lambda e: callback('https://github.com/Tauhid-Ahmed8009'))
        self.dev_youtube_label.place(x=225, y=115)
        self.dev_youtube_label.bind('<Button>', lambda e: callback('https://www.youtube.com/channel/UCYNaebGu6N5_XoszYKVjqXA'))

        def callback(url):
            webbrowser.open_new_tab(url)

    def set_alarm_screen(self):
        self.instruction_lbl.place(x=295, y=50)
        self.calendar.place(x=13, y=7)
        self.time_picker.place(x=320, y=75)
        self.done_btn.place(x=400, y=10)
        self.add_btn.place(x=350, y=135)
        self.home_clock_lbl.configure(font=('Arial', 8))  # mini clock
        self.home_clock_lbl.place(x=295, y=10)
        self.alarm_frame.place_forget()
        self.about_btn.place_forget()
        self.home_cal_lbl.place_forget()
        self.set_menu_btn.place_forget()  # remove the set_menu_btn

    def add(self):
        self.info_msg.place_forget()
        uid = uuid.uuid1()
        date = self.calendar.get_date()
        time = self.time_picker.time()  # returns a tuple (hh, mm, PM)
        hour, minute, period = time  # unpacking time tuple
        hour_24 = hour
        min_for_ts = minute  # avoiding the double digit
        if hour in range(1, 12) and period == 'PM':
            hour_24 += 12
        if hour_24 == 12:
            hour_24 = 0
        if len(str(minute)) == 1:
            minute = f'0{minute}'
        date_time = self.calendar.selection_get()
        alarm_time = f'{hour}:{minute} {period}'
        alarm_timestamp = int(datetime.datetime(date_time.year,  # year 1st
                                                date_time.month,  # month 2nd
                                                date_time.day,  # day 3rd
                                                hour_24,  # hour 4th
                                                min_for_ts)  # min 5th  Sequence for timestamp
                                                .timestamp())
        if alarm_timestamp > self.sys_timestamp:
            alarm_dict = {'id': str(uid), 'date': date, 'time': alarm_time, 'timestamp': alarm_timestamp}
            if len(self.alarms) > 0:
                alarm = [True for i in self.alarms if alarm_time in i.values()]  # LIST COMPREHENSION
                if not any(alarm):  # 'ANY' FUNCTION (returns true if condition met)
                    self.alarms.append(alarm_dict)
                    with open('Alarm_save.json', 'w') as f:
                        json.dump(self.alarms, f)
                else:
                    self.info_msg.configure(text='Alarm already set')
                    self.info_msg.place(x=315, y=160)
            else:
                self.alarms.append(alarm_dict)
                with open('Alarm_save.json', 'w') as f:
                    json.dump(self.alarms, f)
        else:
            self.info_msg.configure(text='Invalid Time')
            self.info_msg.place(x=333, y=160)

    def exit_to_home_menu(self):  # executed after pressing 'done' btn
        # widgets to remove:
        self.calendar.place_forget()
        self.time_picker.place_forget()
        self.info_msg.place_forget()
        self.instruction_lbl.place_forget()
        self.add_btn.place_forget()
        self.done_btn.place_forget()
        self.back_btn.place_forget()
        self.creator_lbl.place_forget()
        self.dev_git_label.place_forget()
        self.dev_youtube_label.place_forget()
        # widgets to replace:
        self.home_clock_lbl.configure(font=('Arial', 35))
        self.home_clock_lbl.place(x=90, y=45)
        self.set_menu_btn.place(x=10, y=10)
        self.home_cal_lbl.place(x=174, y=100)
        self.about_btn.place(x=395, y=10)
        if len(self.alarms) > 0:
            self.refresh_alarm_list()

    def start_up(self):
        try:
            with open('alarm_save.json', 'r') as f:
                data = (json.load(f))
            for alarm in data:
                self.alarms.append(alarm)
        except FileNotFoundError:
            with open('alarm_save.json', 'w') as f1:
                json.dump(self.alarms, f1)
        self.refresh_alarm_list()
        self.home_screen()

    def refresh_alarm_list(self):
        if len(self.alarms) > 0:
            self.alarm_box.delete(0, END)
            # DELETING OBSOLETE ALARMS:
            for i in range(len(self.alarms)):
                ts = self.alarms[i]['timestamp']
                if ts < self.sys_timestamp:
                    del self.alarms[i]
            with open('alarm_save.json', 'w') as f:
                json.dump(self.alarms, f)
            # ADDING UPDATED LIST OF ALARMS:
            for alarm in self.alarms:
                str_to_show = f"{alarm['date']} at {alarm['time']}"
                self.alarm_box.insert(END, str_to_show)
            self.alarm_frame.place(x=10, y=115, width=160, height=79)
        else:
            self.alarm_frame.place_forget()

    def delete_alarm(self, event):
        # DELETING EXECUTED ALARM BY EXECUTION ID:
        selection = event.widget.curselection()
        if selection:
            index = selection[0]
            data = event.widget.get(index)
            to_delete = data.split()
        delete_date = to_delete[0]
        delete_time = f'{to_delete[2]} {to_delete[3]}'
        self.start_count = 0
        for i in range(len(self.alarms)):
            d = self.alarms[i]['date']
            t = self.alarms[i]['time']
            if d == delete_date and t == delete_time:
                del self.alarms[i]
                break
        with open('Alarm_save.json', 'w') as f:
            json.dump(self.alarms, f)
        self.refresh_alarm_list()

    def play_alarm(self):
        for i in range(len(self.alarms)):
            v = self.alarms[i]['id']
            if v == self.executed_alarm_id:
                del self.alarms[i]
                break
        with open('Alarm_save.json', 'w') as f:
            json.dump(self.alarms, f)
        self.alarm_stop_btn.place(x=200, y=140)
        winsound.PlaySound(self.resource_path("PinkPanther.wav"), winsound.SND_LOOP + winsound.SND_ASYNC)
        self.remaining_lbl.place_forget()
        self.refresh_alarm_list()

    def stop_alarm(self):
        winsound.PlaySound(None, winsound.SND_FILENAME)
        self.alarm_stop_btn.place_forget()

    def home_screen(self):
        count_amount = 60
        clock_12 = d.now().strftime("%I:%M:%S %p")
        clock_24 = d.now().strftime("%H:%M:%S")
        cal = d.now().strftime("%d/%m/%Y")
        self.sys_timestamp = int(time.time())
        self.home_cal_lbl.configure(text=cal)
        self.home_clock_lbl.configure(text=clock_12)
        if len(self.alarms) > 0:
            for alarm in self.alarms:
                # CHECKING FOR COUNTER:
                secs_left = int(alarm['timestamp']) - int(self.sys_timestamp)
                if secs_left < 60:
                    self.start_count = secs_left
                # GO TO PLAY ALARM FUNCTION
                if alarm['timestamp'] == self.sys_timestamp:
                    self.executed_alarm_id = alarm['id']
                    self.play_alarm()
        # COUNTER LABEL:
        if self.start_count != 0:
            self.remaining_lbl.configure(text=f'Alarm in {self.start_count} secs')
            self.remaining_lbl.place(x=355, y=175)
            self.start_count -= 1
        else:
            self.remaining_lbl.place_forget()

        self.home_clock_lbl.after(1000, self.home_screen)


root = Tk()
gui = AlarmApp(root)
gui.start_up()
root.mainloop()
