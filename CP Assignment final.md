# Computational Physics Assignment final

## Molecular Dynamics

**Background:** One method of research on many particle system is called molecular dynamics, it depends on wether a micron motion equation is good enough. It consider the movement of all the balls in a box, not an 'average' ball.



**Verlet Method:** Because molecular dynamics calculates the evolution of tons of particles in a huge time scale, the error based on Euler method will accumulate to a large number. Therefore , we need to introduce a new method called Verlet method.

The advantage is, it can keep the energy conservation even in a long time scale.

```math
 x_i(n+1) \approx  2x_i(n)-x_i(n-1)+a_{i,x}(n)(\Delta t)^2\\
\\
&v_{i,x}(n) \approx  \frac{x_i(n+1)-x_i(n-1)}{2\Delta t}\\
\\
&a_{i,x}=\frac{d^2x_i}{dt^2} \approx  \frac{x_i(n+1)+x_i(n-1)-2x_i(n)}{(\Delta t)^2}

```



## Example: Fermi-Pasta-Ulam-Tsingou Model

A group of homogeneous mass particles are connected by identical springs, forming a simple one-dimensional oscillatory system. The two endpoints of the oscillatory system (connected via springs) are attached to rigid walls. 

A computer will simulate the motion process connected by Hooke's law (linear) springs. In this case, the system can be described as simple harmonic oscillation. Each mode behaves as an independent oscillator with its own frequency and amplitude. 

For a one-dimensional FPUT system with linear elasticity, the normal modes are the same as the standing waves of a vibrating string. If the system vibrates in a regular mode, this vibration will continue indefinitely, and the energy remains in the initial mode. In a linear system, each mode behaves as a completely independent oscillator, uncoupled from all other modes.



However, when the elasticity becomes nonlinear, the model becomes complex. It can be viewed as a weak coupling of multiple modes, and this coupling ultimately leads to energy exchange, in accordance with the equipartition theorem.

### Equation

The distance that the `i` point away from initial spot can be described by:
```math
    m_i\frac{d^2x_i}{dt^2}=f_{spring}(x_i+x_{i+1})+f_{spring}(x_i-x_{i-1})
```

The elasticity can be written by:

```math

 f^\alpha_{spring}(\Delta x) = -K(\Delta x)-\alpha(\Delta x)^2   

```

Or  
```math
f^\beta_{spring}(\Delta x) = -K(\Delta x)-\beta(\Delta x)^3    

```

 Set $`m = 1, K = 1 `$ $`\beta`$ shows the intensity of nonlinear property.

```math
x_i(n+1)=2x_i(n)-x_i(n-1)+\frac{1}{(\Delta t)^2}[x_{i+1}(n)+x_{i-1}(n)-2x_i(n)]\\
+\frac{\beta}{(\Delta t)^2}([x_{i+1}(n)-x_i(n)]^3+[x_{i-1}(n)-x_i(n)]^3) 
```

### Phase space

By Fourier transform, we came to the phase space:

```math
a_k=\sqrt{\frac{2}{N+1}}\sum^N_{i=1}x_i\sin\left(\frac{ik\pi}{N+1}\right)
```

The energy of different modes can be expressed by

```math
E_k=\frac{1}{2}\left[ \left( \frac{da_k}{dt}\right)^2 +\omega_k^2a_k^2\right] 
```

The frequency is 

```math
\omega_k=2\sin \left( \frac{k\pi}{2(N+1)}\right)
```

## Image

Compare two videos, we can see the larger $`\beta`$ make system go into chaotic situation faster.

The energy of 1th, 3th, 5th modes by time.![final_1](/image/final_1.png)

![final_2](/image/final_2.png)