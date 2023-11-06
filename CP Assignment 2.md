# Computational Physics Assignment 2

### 1. Make boat floating

**Let the boat traveling on the sea from left to right **

1. When clicking mouse inside the frame, boat appears at that position
2. The routine of boat is circulate

**Code and explaination:**

```lisp
(require 2htdp/image) ;require the package to draw
(require 2htdp/universe) ;require the package to play animation

;;;Definitions
;;Physical Constants;
(define WIDTH-OF-WORLD 420)
;;Graphical
;;THE BOAT
(require 2htdp/image)
(define HEIGHT-OF-BOTTOM 20)
(define a (triangle/ass 90  HEIGHT-OF-BOTTOM 30 "solid" "black"))
(define b (triangle/sas HEIGHT-OF-BOTTOM  90 30 "solid" "black"))
(define c (rectangle 10 HEIGHT-OF-BOTTOM  "solid" "black"))
(define hull-bottom (overlay/offset (overlay/offset c
                  -20 0
                   b)
                   35 0 a))
(define with-line (add-line hull-bottom 35 0 35 -40 "black"))
(define sail (right-triangle 20 30 "solid" "pink"))
;;The boat!!
(define BOAT (overlay/offset with-line
                  10 -15
                  sail))

;;Blacground
(define HEIGHT-OF-WORLD (* (image-height BOAT) 2))
(define SEA (rectangle WIDTH-OF-WORLD 30 "solid" "blue"))

(define BACKGROUND (place-image SEA (/ WIDTH-OF-WORLD 2) (- HEIGHT-OF-WORLD 15)
              (empty-scene WIDTH-OF-WORLD HEIGHT-OF-WORLD "skyblue" )))

(define Y-BOAT (- (- HEIGHT-OF-WORLD 20) (/ (image-height BOAT) 2)))
```

Setting functions

- Define `(tock...)`  to control the speed of the boat: moving on 3 pixels per clock tick, and use `(cold...)` to estimate wether the boat is outside the frame
- Define `(render...)` to place boat on the background 
- Define `(hyper...)` to send mouse position and control the boat
- Use `(check-expect...)` to check the function can run properly

```Lisp
;;;Data Definition
;;clock ticks
(check-expect (tock 89) 92)
(check-expect (tock 64) 67)
(check-expect (tock 500) 0)
(define (tock ws)
  (cond
    [(< ws (+ WIDTH-OF-WORLD (/ (image-width BOAT) 2))) (+ ws 3)]
    [(>= ws (+ WIDTH-OF-WORLD (/ (image-width BOAT) 2))) (- ws ws)]))

;; WorldState -> Image
;; place the car into the BACKGROUND scene,
;; according to the given world state
(check-expect (render  50) (place-image BOAT  50 Y-BOAT BACKGROUND))
(check-expect (render 200) (place-image BOAT 200 Y-BOAT BACKGROUND))

(define (render ws)
  (place-image BOAT ws Y-BOAT BACKGROUND))

;; WorldState Number Number String -> WorldState
;; places the car at the x-coordinate
;; if the given me is "button-down"

(check-expect (hyper 21 10 20 "enter") 21)
(check-expect (hyper 42 10 20 "button-down") 10)
(check-expect (hyper 42 10 20 "move") 42)

(define (hyper x-coordinate x-mouse y-mouse me)
  (cond
    [(string=? "button-down" me) x-mouse]
    [else  x-coordinate]))

;; main : WorldState -> WorldState
;; launch the program from some initial state
;; run: (main 0)
(define (main ws)
   (big-bang ws
             [on-tick tock]
             [on-mouse hyper]
             [to-draw render]))
```

Run it with `(main 0)`

**Screenshot**

![boat_is_flowing](/image/boat_is_flowing.png)

### 2.  Make some waves

**Code**

```lisp
(require 2htdp/image)
(require 2htdp/universe)
;;;Definitions
;;Physical Constants;
(define WIDTH-OF-WORLD 420)
;;Graphical
;;THE BOAT
(require 2htdp/image)
(define HEIGHT-OF-BOTTOM 20)
(define a (triangle/ass 90  HEIGHT-OF-BOTTOM 30 "solid" "black"))
(define b (triangle/sas HEIGHT-OF-BOTTOM  90 30 "solid" "black"))
(define c (rectangle 10 HEIGHT-OF-BOTTOM  "solid" "black"))
(define hull-bottom (overlay/offset (overlay/offset c
                  -20 0
                   b)
                   35 0 a))
(define with-line (add-line hull-bottom 35 0 35 -40 "black"))
(define sail (right-triangle 20 30 "solid" "pink"))
;;The boat!!
(define BOAT (overlay/offset with-line
                  10 -15
                  sail))

(define WAVE1 (polygon (list (make-pulled-point 1 5 60 0 1/2 -10)
                 (make-posn -10 20)
                 (make-pulled-point 1/2 -20 50 0 1/2 20)
                 (make-posn -2 -10))
           "solid"
           "white"))
(define WAVE2 (polygon (list (make-pulled-point 1 5 50 0 1/2 -10)
                 (make-posn -10 20)
                 (make-pulled-point 1/2 -20 50 0 1/2 20)
                 (make-posn -2 -10))
           "solid"
           "white"))
(define BOAT-WITH-WAVE
  (overlay/xy 
  (overlay/xy BOAT
              -5 50
              WAVE2)
  -16 50
  WAVE1))

;;Blacground
(define HEIGHT-OF-WORLD (* (image-height BOAT) 2))
(define SEA (rectangle WIDTH-OF-WORLD 70 "solid" "blue"))
(define BACKGROUND (place-image SEA
                                (/ WIDTH-OF-WORLD 2) (- HEIGHT-OF-WORLD 35)
              (empty-scene WIDTH-OF-WORLD HEIGHT-OF-WORLD "skyblue" )))
(define BACKGROUND-WITH-WAVE
  (add-curve
  (add-curve
  (add-curve
  (add-curve 
  (add-curve BACKGROUND
             30 80 60 1/3
             90 80 60 1/3
             "Medium Cyan")
  120 70 60 1/3
             180 70 60 1/3
             "Medium Cyan")
  160 100 60 1/3
             220 100 60 1/3
             "Medium Cyan")
  240 75 60 1/3
             300 75 60 1/3
             "Medium Cyan")
  330 95 60 1/3
             390 95 60 1/3
             "Medium Cyan"))
  
  
(define Y-BOAT (- (- HEIGHT-OF-WORLD 20) (/ (image-height BOAT) 2)))


;;;Data Definition

;;clock ticks
(check-expect (tock 89) 92)
(check-expect (tock 64) 67)
(check-expect (tock 500) 0)
(define (tock ws)
  (cond
    [(< ws (+ WIDTH-OF-WORLD (/ (image-width BOAT) 2))) (+ ws 3)]
    [(>= ws (+ WIDTH-OF-WORLD (/ (image-width BOAT) 2))) (- ws ws)]))

;; WorldState -> Image
;; place the car into the BACKGROUND scene,
;; according to the given world state

(define (render ws)
  (place-image (rotate (* 180 (/ (atan (* 1 (- (random 2) 0.5)) 3) 3.1415)) BOAT-WITH-WAVE) ws (+ Y-BOAT (* 1 (- (random 2) 0.5))) BACKGROUND-WITH-WAVE))

;; WorldState -> Boolean
;; end boat simulation when car reaches end of world
(check-expect (end? 0) #false)
(check-expect (end? 100) #false)
(check-expect (end? (+ WIDTH-OF-WORLD (/ (image-width BOAT) 2))) #true)

(define (end? ws)
  (>= ws (+ WIDTH-OF-WORLD (/ (image-width BOAT) 2))))


;; WorldState Number Number String -> WorldState
;; places the car at the x-coordinate
;; if the given me is "button-down"

(check-expect (hyper 21 10 20 "enter") 21)
(check-expect (hyper 42 10 20 "button-down") 10)
(check-expect (hyper 42 10 20 "move") 42)

(define (hyper x-coordinate x-mouse y-mouse me)
  (cond
    [(string=? "button-down" me) x-mouse]
    [else  x-coordinate]))


;; main : WorldState -> WorldState
;; launch the program from some initial state
;; run: (main 0)
(define (main ws)
   (big-bang ws
             [on-tick tock 1/5]
             [on-mouse hyper]
             [to-draw render]))
;(main 0)
```

**Image**

![boat_floating_1](/image/boat_floating_1.png)

![boat_floating_2](/image/boat_floating_2.png)
