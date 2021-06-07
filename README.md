## website blocker

import time
from datetime import datetime as dt 
import configparser

#config wordt geladen 
config = configparser.ConfigParser()
config.read("websiteblocker.ini")
config.read("timezone.ini")

#de pad die hij moet openen om websites in op te slaan
host_pad = "C:\WINDOWS\System32\drivers\etc\hosts"
local = "127.0.0.1"

#uit de lijst haal je het hoofdstuk (website) op en uit het hoofdstuk haal je de variabele op
website_list = config.get("website", "website_lijst").split(",")
timezonebegin = int(config.get("timezone", "tijdbegin"))
timezonestop = int(config.get("timezone", "tijdstop"))

while True:
#dt.now kijkt naar huidige tijd
    if dt(dt.now().year, dt.now().month, dt.now().day, timezonebegin) < dt.now() < dt(dt.now().year, dt.now().month, dt.now().day, timezonestop):
        # hij opent host in de r+ mode  staat voor reading mode en writing mode)
        with open(host_pad, "r+") as file:
            #leest de inhoud van het bestand 
            inhoud = file.read() 
            for website in website_list:
                if not website in inhoud:
                    # zo niet dan wordt opgeslagen 
                    file.write(local+ " "+website + "\n")

        print("alle vermelde webistes zijn geblokkerd")
        break
    #als het niet in tussen de timezone bevind verwijder de websites
    else:
        #open het bestand weer
        with open(host_pad, "r+")as file:
            #dit keer leest hij de regels individueel
            inhoud = file.readlines()
            #start van 0 positie
            file.seek(0)

            for line in inhoud:
                #je opent het bestand en leest elke lijn. Als je in de de lijn de website niet vind die we willen blokkeren. schrijf dat lijn.
                if not any(website in line for website in website_list):
                    file.write(line)

            file.truncate()

        print("Websites zijn gedeblokkeerd")
        break
