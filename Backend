import serial
import time
import os
import tkinter as tk
from mido import MidiFile
from pynput import keyboard

TRANSPOSE = -12# tranpose #octaves
BASE_DIR = os.path.dirname(__file__)
program = 1


song_files = [
    f.strip(".mid") for f in os.listdir(os.path.join(BASE_DIR, "Songs"))
    if f.lower().endswith(('.mid', '.midi'))]

root = tk.Tk()
root.title("Simple App")
root.geometry("600x600")
song_list = tk.Listbox(root, borderwidth=2, width=60, height=30, selectmode=tk.SINGLE)
for n, item in enumerate(song_files):
    song_list.insert(n+1, f"{n+1}. {item}")
song_list.pack()

def show_selected():
    global program
    selected_song = song_list.curselection()
    program = selected_song[0]
    root.destroy()

    
button = tk.Button(root, text="Select", command = show_selected)
button.pack()

root.mainloop()

mid = MidiFile(os.path.join(BASE_DIR, "Songs", os.listdir(os.path.join(BASE_DIR, "Songs"))[program]))


print("Compiling...")

ms = round(time.time()*1000.0)
start_time = ms


ser = serial.Serial(port='COM3', baudrate=115200, timeout=1) 
M0_ser = serial.Serial(port = 'COM5', baudrate=115200, timeout=1)
time.sleep(2)

STEP_TRANSPOSITION = 0 #Tranposes the threshold of substepping
MAX_NOTE = 70 # transpose down an octave if exceeding C6
MIN_NOTE = 0 # transpose up an octave if below C1
HALF_STEP_THRESH = 70 + STEP_TRANSPOSITION
QUARTER_STEP_THRESH = 46 + STEP_TRANSPOSITION 
EIGHTH_STEP_THRESH = 30 + STEP_TRANSPOSITION
TICKS_PER_BEAT = mid.ticks_per_beat
TICKS_PER_16TH = TICKS_PER_BEAT // 4

NOTE_NAMES = ["C", "C#", "D", "D#", "E", "F", "F#", "G", "G#", "A", "A#", "B"]

def write_read(command):
    
    ser.write(bytes(command, 'utf-8'))
    time.sleep(0.05)
    data = ser.readline().decode('utf-8').rstrip()
    return data

def midi_to_name(midi_note):
    octave = (midi_note // 12) - 1
    name   = NOTE_NAMES[midi_note % 12]
    return f"{name}{octave}"

def step_range(note):
    if isinstance(note, int):
        if note > HALF_STEP_THRESH:
            return 1
        elif note > QUARTER_STEP_THRESH:
            return 2
        elif note > EIGHTH_STEP_THRESH:
            return 4
        elif note <= EIGHTH_STEP_THRESH:
            return 8
    else:
        return None


def send(motor, note, force_step=None):
    if isinstance(note, int):
        new_step = force_step if force_step is not None else step_range(note)
        if new_step != steps[motor-1]:
            steps[motor-1] = new_step
            M0_cmd = f"M{motor}:{new_step}\n"
            M0_ser.write(M0_cmd.encode())
            ser.write(M0_cmd.encode())
        cmd = f"M{motor}:{midi_to_name(note)}\n"
    else:
        cmd = f"M{motor}:{note}\n"
    ser.write(cmd.encode()) 



def stop_all():
    for i in range(1, 7):
        send(i, "OFF")
    ser.close()
    print("Stopped.")
    os._exit(0)
def on_press(key):
    try:
        if key.char == 'x':
            stop_all()
    except AttributeError:
        if key == keyboard.Key.esc:
            stop_all()


listener = keyboard.Listener(on_press=on_press, daemon=True)
listener.start()  # Non-blocking — runs in background

# build tempo map from tracks
tempo_map = [(0, 500000)] 

for track in mid.tracks:
    abs_tick = 0
    for msg in track:
        abs_tick += msg.time
        if msg.is_meta and msg.type == "set_tempo":
            tempo_map.append((abs_tick, msg.tempo))

tempo_map.sort(key=lambda x: x[0])
print(f"DEBUG tempo map: {tempo_map}")

#convert ticks to ms using the map
def ticks_to_ms(abs_tick):
    ms = 0.0
    prev_tick, prev_tempo = tempo_map[0]
    for tick, tempo in tempo_map[1:]:
        if abs_tick <= tick:
            break
        ms += (tick - prev_tick) * prev_tempo / mid.ticks_per_beat / 1000
        prev_tick, prev_tempo = tick, tempo
    ms += (abs_tick - prev_tick) * prev_tempo / mid.ticks_per_beat / 1000
    return int(ms)

#use ticks_to_ms when building events (store abs_tick, convert later)
events = []
for track in mid.tracks:
    abs_tick = 0
    for msg in track:
        abs_tick += msg.time
        if msg.is_meta:
            continue
        if msg.channel == 9:
            continue
        if msg.type not in ("note_on", "note_off"):
            continue
        is_on = msg.type == "note_on" and msg.velocity > 0
        tsp_note = msg.note + TRANSPOSE
        if tsp_note > MAX_NOTE:
            while tsp_note > MAX_NOTE:
                tsp_note -= 12
        elif tsp_note < MIN_NOTE:
            while tsp_note < MIN_NOTE:
                tsp_note += 12
        events.append((ticks_to_ms(abs_tick), is_on, tsp_note))

events.sort(key=lambda x: x[0])

print(events)

lanes = [None, None, None, None, None, None]
steps = [1,1,1,1,1,1]

def assign_lane(note):
    # find a free lane
    for i in range(5):
        if lanes[i] is None:
            lanes[i] = note
            send(i + 1, note)
            return
    #All lanes full
    evict = 4
    lanes[evict] = note
    send(evict + 1, note)



def release_lane(note):
    for i in range(5):
        if lanes[i] == note:
            lanes[i] = None
            send(i + 1, "OFF")
            return
        
def assign_melody():
    highest_note = max(
        (n for n in lanes[:5] if n is not None),
        default=None
    )
    if lanes[5] == highest_note:
        return
    lanes[5] = highest_note
    send(6, highest_note if highest_note is not None else "OFF")
                


input("Press Enter to start playback, press X or ESC anytime to quit: ")
print("Playing...")

start_ms = time.time() * 1000


for time_ms, is_on, note in events:

    now  = time.time() * 1000
    wait = (start_ms + time_ms) - now
    if wait > 0:
        time.sleep(wait / 1000)
        print([midi_to_name(n) if n is not None else None for n in lanes], lanes, steps)
    if is_on:
        assign_lane(note)
        assign_melody()
    else:
        release_lane(note)
        assign_melody()

# stop all motors at end
for i in range(1, 7):
    send(i, "OFF")

ser.close()
print("Done")
