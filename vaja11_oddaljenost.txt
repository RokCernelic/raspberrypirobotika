#!/usr/bin/env python

# ==========
# knjiznice
# ==========
import RPi.GPIO as GPIO
import time
from time import sleep


# ===========
# nastavitve
# ===========
GPIO.setmode(GPIO.BCM)

# nastavitev TRIGGER in ECHO pina
GPIO_TRIGGER = 18
GPIO_ECHO = 24

# ECHO je INput TRIGGER je OUTput (IN / OUT)
GPIO.setup(GPIO_TRIGGER, GPIO.OUT)
GPIO.setup(GPIO_ECHO, GPIO.IN)


# =========
# funkcije
# =========
def distance():
    # set Trigger to HIGH
    GPIO.output(GPIO_TRIGGER, 1)

    # set Trigger after 0.01ms to LOW
    sleep(0.00001)
    GPIO.output(GPIO_TRIGGER, 0)

    StartTime = time.time()
    StopTime = time.time()

    # save StartTime
    while GPIO.input(GPIO_ECHO) == 0:
        StartTime = time.time()

    # save time of arrival
    while GPIO.input(GPIO_ECHO) == 1:
        StopTime = time.time()

    # time difference between start and arrival
    TimeElapsed = StopTime - StartTime
    # multiply with the sonic speed (34300 cm/s)
    # and divide by 2, because there and back
    distance = (TimeElapsed * 34300) / 2

    return distance


# ========
# program
# ========
try:
   while True:
      razdalja = distance()
      print ("Measured Distance = %.1f cm" % razdalja)
      sleep(0.1)

except KeyboardInterrupt:
   print("Uporabnik je prekinil meritev.")
   GPIO.cleanup()
