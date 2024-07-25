# VOICE-ASSISTENT
import speech_recognition as sr
import pyttsx3
import datetime
import requests
from bs4 import BeautifulSoup

# Initialize the recognizer and text-to-speech engine
recognizer = sr.Recognizer()
tts_engine = pyttsx3.init()

# Set TTS properties
voices = tts_engine.getProperty('voices')
tts_engine.setProperty('voice', voices[0].id)  # Use the first available voice
tts_engine.setProperty('rate', 150)  # Speed up the speaking rate
def speak(text):
    tts_engine.say(text)
    tts_engine.runAndWait()

def listen():
    with sr.Microphone() as source:
        recognizer.adjust_for_ambient_noise(source, duration=0.5)
        print("Listening...")
        audio = recognizer.listen(source)
        try:
            command = recognizer.recognize_google(audio)
            print(f"You said: {command}")
            return command.lower()
        except sr.UnknownValueError:
            speak("Sorry, I did not understand that.")
            return ""
            except sr.RequestError:
            speak("Sorry, my speech service is down.")
            return ""

def tell_time():
    now = datetime.datetime.now()
    time_str = now.strftime("%H:%M")
    speak(f"The current time is {time_str}")

def tell_date():
    today = datetime.date.today()
    date_str = today.strftime("%B %d, %Y")
    speak(f"Today's date is {date_str}")

def search_web(query):
    url = f"https://www.google.com/search?q={query}"
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) 
        Chrome/58.0.3029.110 Safari/537.3'}
    response = requests.get(url, headers=headers)
    soup = BeautifulSoup(response.text, 'html.parser')

    try:
        answer = soup.find('div', class_='BNeawe').text
        speak(f"The answer is: {answer}")
        print(f"The answer is: {answer}")
    except AttributeError:
        speak("Sorry, I couldn't find the answer.")
        print("Sorry, I couldn't find the answer.")

def main():
    speak("Hello, I am your voice assistant. How can I help you today?")
    
    while True:
        command = listen()
        
        if "hello" in command:
            speak("Hello Malay.. How can I assist you?")
        elif "time" in command:
            tell_time()
        elif "Malay" in command:
            speak("Yes, all  checked")
        elif "date" in command:
            tell_date()
        elif "search" in command:
            speak("What would you like to search for?")
            query = listen()
            if query:
                search_web(query)
        elif "exit" in command or "quit" in command:
            speak("Goodbye!")
             break
        else:
            speak("I'm sorry, I can only respond to commands related to saying hello, telling the time, date, or searching the web. Please try again.")

if _name_ == "_main_":
    main()
