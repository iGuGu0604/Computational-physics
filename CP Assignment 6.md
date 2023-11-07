# Computational Physics Assignment 6

Finish 3 problems in the textbook:

`Euler` and `Euler-Cromer` are good methods to numerically simulate a simple pendulum. 

The motion equation of a simple pendulum can be written as 

'''math

 \frac{\mathrm{d}^{2} \theta}{\mathrm{d} t^{2}}=-\frac{g}{l} \sin \theta-q \frac{\mathrm{d} \theta}{\mathrm{d} t}+F_{D} \sin \left(\Omega_{D} t\right) 

'''

$`-q\frac{d\theta}{dt}`$ is friction and $`F_D\sin(\Omega_Dt)`$ can be driving force term. By simulation, we can know, given proper parameters the system can reach to a state with stable period and amptitude.



## 3.7 Calculate $\frac{\mathrm{d}F}{\mathrm{d}\theta} $ by time

```Lisp
#lang racket
(require 2htdp/image)
(require 2htdp/universe)
(require test-engine/racket-tests)
(require (prefix-in plot: plot))
;1.define a structure to descibe angle, time and angular velocity
(define-struct pos [theta omega t])
; pos: pendulum oscillation status
; (make-pos Number Number Number)
;      pos-theta
;      pos-omega
;      pos-t
(define G/L 1.0)
(define Q 0.5)
(define FD 1.2)
(define OmegaD (/ 2.0 3.0))
(define DT 0.04)
(define (next-moment-all current-pos [method 'euler])
  (let ([theta (pos-theta current-pos)]
        [omega (pos-omega current-pos)]
        [t (pos-t current-pos)])
    (cond
      [(equal? method 'euler)
       (make-pos (+ theta (* DT omega))
                 (- omega (* DT theta G/L))
                 (+ t DT))]
      [(equal? method 'euler-cromer)
       (let ([new-omega (- omega (* DT theta G/L))])
         (make-pos (+ theta (* DT new-omega))
                   new-omega
                   (+ t DT)))]
      [(equal? method 'euler-cromer-chaos)
       (let ([new-omega (- omega (* DT (sin theta) G/L) (* DT Q omega) (* -1 DT FD (sin (* t OmegaD))))])
         (make-pos (+ theta (* DT new-omega))
                   new-omega
                   (+ t DT)))]       
      [else (error 'invalid-method)])))

(define (the-next-moment method)
  (lambda (x) (next-moment-all x method)))
;initial state
(define (init-pos theta )
  (make-pos (/ (* theta pi) 180.)
            0
            0))
; cnn Func Func -> [List-of cnn]
; start to iterate
(define (record init-state nexter end?)
  (let _ ([previous-states '()]
          [current-state init-state])
    (if (end? current-state)
        (cons current-state previous-states)
        (_ (cons current-state previous-states) (nexter current-state)))))

(define (extract vt-pattern states)
  (let _ ([vts '()]
          [sts states])
    (if (empty? sts)
        vts
        (_ (cons (vt-pattern (first sts)) vts) (rest sts)))))
(define (t-vs-theta the-pos)
  (vector (pos-t the-pos) (* (/ (pos-theta the-pos) pi) 180)))

;set stop condition
(define (stop-after h)
         (lambda (x) (> (pos-t x) h)))

(define (calculate-delta-theta theta1 theta0)
  (map (lambda (x y)
         (vector (vector-ref x 0) (abs (- (vector-ref x 1) (vector-ref y 1))))) theta1 theta0))  

(define (osillatory-points my-pos [method 'euler])
  (plot:lines
   (extract t-vs-theta
            (record my-pos (the-next-moment method) (stop-after 20)))
   #:label "Simple Pendulum -- Euler Method"))
(print  (extract t-vs-theta
                (record (init-pos 0.201) (the-next-moment 'euler-cromer-chaos) (stop-after 5))))
(print (calculate-delta-theta
        (extract t-vs-theta
                (record (init-pos 0.201) (the-next-moment 'euler-cromer-chaos) (stop-after 5)))
        (extract t-vs-theta
                (record (init-pos 0.2) (the-next-moment 'euler-cromer-chaos) (stop-after 5)))))
;draw the diagram
(define plot-delta-thera
  (plot:lines
   (calculate-delta-theta
(extract t-vs-theta
                (record (init-pos 0.201) (the-next-moment 'euler-cromer-chaos) (stop-after 50)))
        (extract t-vs-theta
                (record (init-pos 0.2) (the-next-moment 'euler-cromer-chaos) (stop-after 50))))
   #:label "t-vs-delta(theta) Force_d=1.2"
   #:y-min 1e-7 #:y-max 1e+1
   #:color "black"
   ))
(parameterize ([plot:plot-y-transform plot:log-transform]
                 [plot:plot-y-ticks   (plot:log-ticks)])
  (plot:plot plot-delta-thera))

```

![3_9](/image/3_9.png)

## 3.8 Relationship between $'\Omega'$ and $`\theta`$

**Code**

```Lisp
# lang racket
(require 2htdp/image)
(require 2htdp/universe)
(require test-engine/racket-tests)
(require (prefix-in plot: plot))

(define-struct pos [theta omega t])

(define G/L 1.0)
(define Q 0.5)
(define FD 0.5)
(define OmegaD (/ 2.0 3.0))
(define DT 0.04)
(define (next-moment-all current-pos [method 'euler])
  (let ([theta (pos-theta current-pos)]
        [omega (pos-omega current-pos)]
        [t (pos-t current-pos)])
    (cond
      [(equal? method 'euler)
       (make-pos (+ theta (* DT omega))
                 (- omega (* DT theta G/L))
                 (+ t DT))]
      [(equal? method 'euler-cromer)
       (let ([new-omega (- omega (* DT theta G/L))])
         (make-pos (+ theta (* DT new-omega))
                   new-omega
                   (+ t DT)))]
      [(equal? method 'euler-cromer-chaos)
       (let* ([new-omega (- omega (* DT (sin theta) G/L) (* DT Q omega) (* -1 DT FD (sin (* t OmegaD))))]
              [new-theta (+ theta (* DT new-omega))])
         (make-pos (cond [(> new-theta pi) (- new-theta (* 2 pi))]
                         [(< new-theta (- 0 pi)) (+ new-theta (* 2 pi))]
                         [else new-theta])
                   new-omega
                   (+ t DT)))]       
      [else (error 'invalid-method)])))
(define (the-next-moment method)
  (lambda (x) (next-moment-all x method)))


(define (init-pos theta)
  (make-pos theta
            0
            0))

(define (record init-state nexter end?)
  (let _ ([previous-states '()]
          [current-state init-state])
    (if (end? current-state)
        (cons current-state previous-states)
        (_ (cons current-state previous-states) (nexter current-state)))))
;从状态中提取向量并组合成列表
(define (extract vt-pattern states)
  (let _ ([vts '()]
          [sts states])
    (if (empty? sts)
        vts
        (_ (cons (vt-pattern (first sts)) vts) (rest sts)))))
(define (theta-vs-omega the-pos)
  (vector (pos-theta the-pos) (pos-omega the-pos)))


(define (stop-after h)
         (lambda (x) (> (pos-t x) h)))

(define (osillatory-points my-pos [method 'euler])
  (plot:points
   (extract theta-vs-omega
            (record my-pos (the-next-moment method) (stop-after 80)))
   #:sym 'dot
   #:x-min -1.0	 
   #:x-max +1.0	 
   #:y-min -1.0	 
   #:y-max 1.0
   #:label "Force_d=0.5"
))
  (plot:plot (osillatory-points (init-pos 0.2) 'euler-cromer-chaos))
```

![3_8](/image/3_8.png)

## 3.9

`Runge-Kutta-Method`：consider more points‘ slops in $[x_n,x_{n+1}]$ ，the average slope$`K`$ can be calculated from the weighted sum $K$，it can increase the accuracy.

```math


  \omega_{n+1}=\omega_n +(k_1+2k_2+2k_3+k_4)\frac{dt}{6} \\k_1=f(\omega_n,t_n) \\k_2=f(\omega_n+k_1\frac{dt}{2},t_n+\frac{dt}{2}) \\k_3=f(\omega_n+k_2\frac{dt}{2},t_n+\frac{dt}{2}) \\k_4=f(\omega_n+k_3dt,t_n+dt)

```

![3_7](/image/3_7.png)
