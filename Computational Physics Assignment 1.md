# Computational Physics Assignment 1

### 1. Draw a UFO and let it land on the flat rock

**Code and explaination:**

```lisp
;This line is a example of explaination

(require 2htdp/image) ;require the package to draw
(require 2htdp/universe) ;require the package to play animation

;Define a ufo and draw it
(define ufo (overlay (circle 10 "solid" "green")
         (rectangle 40 4 "solid" "green")))
(define H 200)
(define W 100)
(define bedrock (rectangle W 10 "solid" "brown"))
(define back (place-image bedrock (/ W 2) (- H 5)
              (empty-scene W H "skyblue")))
 
;Define the process of landing 
(define ufo-center-to-bottom (- H 20))
(define (UFO-coming height)
  (cond
    [(<= height ufo-center-to-bottom)
     (place-image ufo 50 height
                  back)]
    [(> height ufo-center-to-bottom)
     (place-image ufo 50 ufo-center-to-bottom
                  back)]))

;Display the animation
(animate UFO-coming)
```

**Screenshot**

<img src="/Users/simones/Desktop/ufo_1.png" alt="ufo_1" style="zoom:50%;" />



<img src="/Users/simones/Desktop/ufo_2.png" alt="ufo_2" style="zoom:50%;" />

<img src="/Users/simones/Desktop/ufo_4.png" alt="ufo_4" style="zoom:50%;" />

### 2. Define a boat

**Code**

```lisp
(require 2htdp/image)
(define a (triangle/ass 90  30 60 "solid" "black"))
(define b (triangle/sas 30  90 60 "solid" "black"))
(define c (rectangle 60 30  "solid" "black"))
(define hull-bottom (overlay/offset (overlay/offset c
                  -60 0
                  b)
                  90 0 a))
(define with-line (add-line hull-bottom 90 0 90 -90 "black"))
(define sail (right-triangle 60 70 "solid" "pink"))
(define boat (overlay/offset with-line
                  30 -30
                  sail))
```

**Image**

![a_boat](/Users/simones/Desktop/a_boat.png)