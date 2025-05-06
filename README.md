# language_translator
# -*- coding: utf-8 -*-
import speech_recognition as sr
from deep_translator import GoogleTranslator
from gtts import gTTS
from playsound import playsound
import os

# Initialize speech recognizer
recognizer = sr.Recognizer()

# Function: Speech input to text
def speech_to_text():
    with sr.Microphone() as source:
        print("Clearing background noise...")
        recognizer.adjust_for_ambient_noise(source, duration=1)
        print("Listening...")
        try:
            audio = recognizer.listen(source, timeout=10)
        except sr.WaitTimeoutError:
            print("Timeout reached. No speech detected.")
            return None
        print("Recognizing...")
        try:
            text = recognizer.recognize_google(audio, language='en')
            print(f"Recognized Text: {text}")
            return text
        except Exception as e:
            print(f"Error recognizing speech: {e}")
            return None

# Function: Text to translated speech using gTTS
def text_to_speech(text, lang='en'):
    try:
        tts = gTTS(text=text, lang=lang)
        tts.save("output.mp3")
        playsound("output.mp3")
        os.remove("output.mp3")
    except Exception as e:
        print(f"Error in text-to-speech: {e}")

# Function: Translate text
def translate_text(text, target_lang):
    try:
        translated = GoogleTranslator(source='auto', target=target_lang).translate(text)
        return translated
    except Exception as e:
        print(f"Translation error: {e}")
        return None

# Main menu logic
def main():
    print("Choose the operation you want to perform:")
    print("1. Speech-to-Speech")
    print("2. Text-to-Speech (with translation)")
    print("3. Text-to-Text")
    
    choice = input("Enter your choice (1/2/3): ")

    if choice == '1':
        print("\n--- Speech-to-Speech Selected ---")
        original_text = speech_to_text()
        if original_text:
            target_lang = input("Enter target language code:")
            translated = translate_text(original_text, target_lang)
            if translated:
                print(f"Translated Text: {translated}")
                text_to_speech(translated, target_lang)

    elif choice == '2':
        print("\n--- Text-to-Speech Selected ---")
        original_text = input("Enter the text you want to convert to speech: ")
        target_lang = input("Enter target language code: ")
        translated = translate_text(original_text, target_lang)
        if translated:
            print(f"Translated Text: {translated}")
            text_to_speech(translated, target_lang)

    elif choice == '3':
        print("\n--- Text-to-Text Selected ---")
        original_text = input("Enter the text you want to translate: ")
        target_lang = input("Enter target language code")
        translated = translate_text(original_text, target_lang)
        if translated:
            print(f"Translated Text: {translated}")

    else:
        print("Invalid choice. Please enter 1, 2, or 3.")

# Run the script
if __name__ == "__main__":
    main()
