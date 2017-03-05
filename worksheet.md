# Stop Motion Film

Drehe deinen eigenen Stop-Motion-Film mit dem Raspberry Pi.

Du kannst Lego dazu benutzen um den Bau eines Turms zu filmen, oder einen Kampf zwischen den Figuren - was du willst.

## Schließe die Kamera an

Bevor Du den Pi hochfährst, musst du die Kamera anschließen.

1. Neben dem Ethernet Port ist der Kameraport. Lockere den Verschluß.

1. Stecke den Streifen in den Port, mit der blauen Seite zum Ethernet port. Halte den Streifen fest und schiebe den Verschluss zu.

1. Schließe den Strom an und fahre den Pi hoch.

![Connect the camera](images/connect-camera.jpg)

## Teste die Kamera

1. Öffne ein Terminalfenster und tippe folgendes Kommando ein:

    ```bash
    raspistill -k
    ```

1. Du solltest ein Vorschaubild auf dem Bildschirm sehen. Es ist egal ob es auf dem Kopf steht. Drücke `Strg + C` um die VOrschau zu verlassen.

1. Gib 'ls' ein um die die Dateien in deinem Homeverzeichnis anzeigen zu lassen. Du solltest ein Bild 'image1.jpg' dort sehen. 

1. Klicke auf den Dateimanager und finde das Bild darin. Doppelklicke auf `image1.jpg` um es anzusehen.

## Nimm ein Bild auf mit Python

1. Öffne **Python 3 (IDLE)**aus dem Hauptmenu:

    ![Open Python 3](images/python3-app-menu.png)

1. Wähle `File > New Window` aus dem Menu, um einen Python Editor zu starten.

1. Gib die folgenden Zeilen ein (auf Groß- und Kleinschreibung achten!):

    ```python
    from picamera import PiCamera
    from time import sleep

    camera = PiCamera()

    camera.start_preview()
    sleep(3)
    camera.capture('/home/pi/Desktop/image.jpg')
    camera.stop_preview()
    ```

1. Wähle `File > Save` aus dem Menü und speichere die Datei als `animation.py`.

1. Drücke `F5`, um das Skript zu starten.

1. Du solltest eine Datei `image.jpg` auf dem Desktop finden. Doppelklicke sie, um das Bild zu öffnen.

1. Wenn das Bild verkehrtherum ist, kannst du entweder die Kamera umdrehen, oder Python das Bild drehen lassen. Dafür gib die folgenden Zeilen 

    ```python
    camera.rotation = 180
    ```

    hinter `camera = PiCamera()` ein, so dass dein Programm so aussieht:

    ```python
    from picamera import PiCamera
    from time import sleep

    camera = PiCamera()

    camera.rotation = 180
    camera.start_preview()
    sleep(3)
    camera.capture('/home/pi/Desktop/image.jpg')
    camera.stop_preview()
    ```

1. Lass das SKript noch einmal laufen und es wird `image2.jpg` mit einem neuen Bild in korrekter AUsrichtugn überschreiben. Lass diese Zeilen im Skript, wenn Du weiterarbeitest.

## Füge ein Tastatursignal hinzu

1. Ändere den `sleep`Befehl so, daß das Programm auf einen Tastendruck wartet:

    ```python
    from picamera import PiCamera
    from time import sleep

    camera = PiCamera()

    camera.start_preview()
    input("Drücke eine Taste...")
    camera.capture('/home/pi/image3.jpg')
    camera.stop_preview()
    ```

1. Speichere dein Skript und lass es laufen.

1. Wenn die Vorschau begonnen hat, drücke eine Taste, um ein Bild zu machen.

1. Gehe zurück zum Dateimanager und suche nach dem Bild `image3.jpg`. Doppelklicke es zum Ansehen.

## Mach ein Selfie.

Wenn Du ein Selfie machen willst, brauchst du noch eine Verzögerung, damit Du dich in Position bringen kannst. Das fügst du so zu deinem Skript hinzu:

1. Füge eine Zeile hinzu, die das Programm anweist, kurz zu warten:

    ```python
    camera.start_preview()
    input("Drücke eine Taste...")
    sleep(3)
    camera.capture('/home/pi/Desktop/image3.jpg')
    camera.stop_preview()
    ```

1. Speichere das SKript und lass es laufen.

1. Drück eine Taste und mache ein Selfie.

1. Du kannst das Bild wieder durch Doppelklicken ansehen.

## Stop-Motion Animation

Now that you have successfully taken individual photographs with your camera, it's time to try combining a series of still images to make a stop motion animation.

1. **IMPORTANT** You must create a new folder to store your stills. In the terminal window, enter `mkdir animation`.

1. Modify your code to add a loop to keep taking pictures every time the button is pressed:

    ```python
    camera.start_preview()
    frame = 1
    while True: 
        try:
            button.wait_for_press()
            camera.capture('/home/pi/animation/frame%03d.jpg' % frame)
            frame += 1
        except KeyboardInterrupt:
            camera.stop_preview()
            break
    ```

    *Because `while True` goes on forever, you have to be able to make it exit gracefully. Using `try` and `except` means it can deal with an exceptional circumstance - if you force it to stop with `Ctrl + C` it will close the camera preview and exit the loop*

    *`frame%03d` means the file will be saved as the name "frame" followed by a 3-digit number with leading zeroes - 001, 002, 003, etc. This allows them to be easily sorted into the correct order for the video.*

1. Now set up your animation subject (e.g. LEGO), ready to start the stop motion animation.

1. Press the button to capture the first frame, then rearrange the animation subject and press the button again to capture each subsequent frame.

1. Once all the frames have been captured, press `Ctrl + C` to terminate the program.

1. Open the `animation` folder in the file manager to see your stills collection.

## Generate the video

1. To generate the video, begin by returning to the terminal window.

1. Run the video rendering command:

    ```bash
    avconv -r 10 -i animation/frame%03d.jpg -qscale 2 animation.mp4
    ```

    *Note you're using `%03d` again - this is a common format which both Python and `avconv` understand, and means the photos will be passed in to the video in order.*

1. You can adjust the frame rate by editing the rendering command. Try changing `-r 10` (10 frames per second) to another number.

    ```bash
    omxplayer animation.mp4
    ```

1. You can also change the filename of the rendered video to stop it from overwriting your first attempt. To do this, change `animation.mp4` to something else.

## What next?

- Why not share your video? Try uploading it to YouTube!
- Now you know how to wire up a button to take a picture with the camera module, what else could you use this for?
- Could you do something similar for a time-lapse video?
- What could you use instead of a button? A motion sensor?
- Instead of making a video, what else could you do with photos taken with the camera module? You could post them to Twitter, or another social media site.
