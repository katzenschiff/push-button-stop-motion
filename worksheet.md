# Sop Motion Film

Drehe deinen eigenen Stop-Motion-Film mit dem Raspberry Pi.

Du kannst Lego dazu benutzen um den Bau eines Turms zu filmen, oder einen Kampf zwischen den Figuren - was du willst.

## Schließe die Kamera an

Bevor Du den Pi hochfährst, musst du die Kamera anschließen.

1. Neben dem Ethernet Port ist der Kameraport. Lockere den Verschluß.

1. Stecke den Streifen in den Port, mit der blauen Seite zum Ethernet port. Halte den Streifen fest und schiebe den Verschluss zu.

1. Schließe den Strom an und fahre den Pi hoch.

![Connect the camera](images/connect-camera.jpg)

## Tests die Kamera

1. Öffne ein Terminalfenster und tippe folgendes Kommando ein:

    ```bash
    raspistill -k
    ```

1. Du solltest ein Vorschaubild auf dem Bildschirm sehen. Es ist egal ob es auf dem Kopf steht. Drücke `Strg + C` um die VOrschau zu verlassen.

1. Gib 'ls' ein um die die Dateien in deinem Homeverzeichnis anzeigen zu lassen. Du solltest ein Bild 'image1.jpg' dort sehen. 

1. Klicke auf den Dateimanager und finde das Bild darin. Doppelklicke auf `image1.jpg` um es anzusehen.

## Nimm ein Bild auf mit Python

1. Open **Python 3 (IDLE)** from the main menu:

    ![Open Python 3](images/python3-app-menu.png)

1. Select `File > New Window` from the menu to open a Python file editor.

1. Carefully enter the following code into the new window (case is important!):

    ```python
    from picamera import PiCamera
    from time import sleep

    camera = PiCamera()

    camera.start_preview()
    sleep(3)
    camera.capture('/home/pi/Desktop/image.jpg')
    camera.stop_preview()
    ```

1. Select `File > Save` from the menu (or press `Ctrl + S`) and save as `animation.py`.

1. Press `F5` to run the script.

1. You should see `image.jpg` saved on your Desktop. Double-click the icon to open the image.

1. If the picture is upside-down you can either reposition your camera using a mount, or leave it as it is and tell Python to flip the image. To do this, add the following lines:

    ```python
    camera.rotation = 180
    ```

    after `camera = PiCamera()`, so it becomes:

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

1. Run the file again and it will overwrite `image2.jpg` with a new image in the correct orientation. Remember to keep these lines in your code while you alter it in the next few steps.

## Connect a hardware button

1. Using your breadboard and jumper leads, connect the Pi to the button as shown in the diagram below:

    ![](images/picamera-gpio-setup.png)

1. Import `Button` from the `gpiozero` module at the top of the code, create up a `Button` connected to pin 17, and change the `sleep` line to use `button.wait_for_press` like so:

    ```python
    from picamera import PiCamera
    from time import sleep
    from gpiozero import Button

    button = Button(17)
    camera = PiCamera()

    camera.start_preview()
    button.wait_for_press()
    camera.capture('/home/pi/image3.jpg')
    camera.stop_preview()
    ```

1. Save and run your script.

1. Once the preview has started, press the button connected to your Pi to capture an image.

1. Return to the file manager window and you should see your `image3.jpg`. Again, double-click to view.

## Take a selfie

If you want to take a photograph of yourself with the camera board, you are going to have to add in a delay to enable you to get into position. You can do this by modifying your program.

1. Add a line to your code to tell the program to sleep briefly before capturing an image, as below:

    ```python
    camera.start_preview()
    button.wait_for_press()
    sleep(3)
    camera.capture('/home/pi/Desktop/image3.jpg')
    camera.stop_preview()
    ```

1. Save and run your script.

1. Press the button and try to take a selfie. Be sure to keep the camera still! Ideally, it should be mounted in position.

1. Again, feel free to check the image in the file manager. You can run the program again to take another selfie.

## Stop motion animation

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
