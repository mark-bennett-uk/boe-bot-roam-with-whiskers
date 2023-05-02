# Parallax Boe-Bot roam with whiskers to avoid obstacles.

Hardware

    Parallax Boe-Bot
    2 x Servo Motors [pins 12 and 13]
    2 x Wire Whiskers [pins 5 and 7]
    2 x Red LEDs (with suitable resistors as in the Student Guide) [pins 1 and 10]
    Piezo Speaker [pin 4]
    
Code
      ' Roam with Whiskers

      ' {$STAMP BS2}
      ' {$PBASIC 2.5}

      DEBUG "Program Start..."

      speed      VAR Byte ' speed + or - difference from 750
      distance   VAR Word ' distance to move (mm)
      direction  VAR Nib  ' direction 3 = forward/1 = backward
      turn       VAR Byte ' turn rate 120 = no turn 240 = hard right, 0 = hard left

      repeat     VAR Byte ' repeat, used for various features
      counter    VAR Word ' count, used for various features

      ' start warning sound
      PAUSE 500 : repeat = 4 : GOSUB alarm

      DO
        IF (IN5 = 0) AND (IN7 = 0) THEN ' Both whiskers detect obstacle
          HIGH 1
          HIGH 10
          distance = 180 : speed = 30 : direction = 1 : turn = 120 : GOSUB move
          distance = 120 : speed = 30 : direction = 3 : turn = 0 : GOSUB move
        ELSEIF (IN5 = 0) THEN ' Left whisker contacts
          LOW 1
          HIGH 10
          distance = 130 : speed = 30 : direction = 1 : turn = 120 : GOSUB move
          distance = 50 : speed = 30 : direction = 1 : turn = 80 : GOSUB move
          distance = 50 : speed = 30 : direction = 3 : turn = 160 : GOSUB move
        ELSEIF (IN7 = 0) THEN ' Right whisker contacts
          HIGH 1
          LOW 10
          distance = 130 : speed = 30 : direction = 1 : turn = 120 : GOSUB move
          distance = 50 : speed = 30 : direction = 1 : turn = 160 : GOSUB move
          distance = 50 : speed = 30 : direction = 3 : turn = 80 : GOSUB move
        ELSE ' Both whiskers 1, no contacts
          LOW 1
          LOW 10
          distance = 10 : speed = 30 : direction = 3 : turn = 120 : GOSUB move
        ENDIF ' and check again
      LOOP

      END

      ' sub programs
      alarm:
        FOR counter = 1 TO repeat
          FREQOUT 4, 100, 3000
          PAUSE 200
        NEXT
        RETURN

      move:
        repeat = (41 * distance) / 130
        FOR counter = 1 TO repeat
          PULSOUT 13, 750 + ((speed + (turn - 120)) * (direction - 2))
          PULSOUT 12, 750 - ((speed - (turn - 120)) * (direction - 2))
          PAUSE 20
        NEXT
        RETURN
