# Lorenz Attractor
A single-line 3D lorenz attractor rendered on a 2D canvas. A lorenz attractor is a 3D shap resembling a butterfly that visualizes the chaotic, non-repeating path of a simplified weather and fluid-flow system over time.

# Usage
Open the `main.html` file in a browser, or paste this
```html
data:text/html,<style>body{margin:0;background-color:black};canvas{width:100vw;height:100vh;display:block;touch-action:none};</style><canvas id="la"></canvas><script>const ctx=la.getContext("2d");let w,h,a;function resize(){const d=window.devicePixelRatio||1;w=Math.round(d*innerWidth);h=Math.round(d*innerHeight);la.width=w;la.height=h;a=Math.min(w/70,h/60);};resize();onresize = resize;la.onpointerdown=e=>{drag=true;lx=e.clientX;ly=e.clientY;la.setPointerCapture(e.pointerId);};la.onpointermove=e=>{if (!drag) return;th += 0.005*(e.clientX-lx);ph += 0.005*(e.clientY-ly);ph = Math.max(-1.5, Math.min(1.5, ph));ly = e.clientY;lx = e.clientX;};la.onpointerup=la.onpointercancel=()=>{drag=false;};const b=8/3,s=10,r=28,dt=0.005,zc=27,D=120,N=4000;let x=1,y=1,z=1,th=0,ph=0,drag=false,lx,ly,n=0;const X=new Float32Array(N),Y=new Float32Array(N),Z=new Float32Array(N);function step(){for(let i=0;i<20;i++){n+=1;const dx=s*(y-x),dy=x*(r-z)-y,dz=x*y-b*z;x+=dx*dt;y+=dy*dt;z+=dz*dt;X[n%N]=x;Y[n%N]=y;Z[n%N]=z;}};function draw(){let px,py;const co=Math.min(n,N),cs=Math.cos(th),sn=Math.sin(th),cp=Math.cos(ph),sp=Math.sin(ph);ctx.clearRect(0,0,w,h);ctx.lineCap="round";for(let k=0;k<co;k++){const i=(n-k+N)%N,xr=X[i]*cs-Y[i]*sn,yr=X[i]*sn+Y[i]*cs,zr=Z[i]-zc,dp=yr*cp-zr*sp,up=yr*sp+zr*cp,f=D/(D+dp),sh=Math.max(0,Math.min(1,(f-0.75)/0.65)),sx=a*xr*f+w/2,sy=h/2-a*up*f;if(k>0){ctx.beginPath();ctx.moveTo(px,py);ctx.lineTo(sx,sy);ctx.lineWidth=2*devicePixelRatio*f;ctx.strokeStyle=`hsl(${360*(X[i]+30)/60},100%,${40+20*sh}%)`;ctx.globalAlpha=(1-k/co)*(0.6+0.4*sh);ctx.stroke();}px=sx;py=sy;}ctx.globalAlpha=1;};function frame(){step();if(!drag)th+=0.01;draw();requestAnimationFrame(frame);};requestAnimationFrame(frame);</script>
```
into the address bar.

# Model
The system integrated is
$$
\begin{aligned}
\frac{dx}{dt} = \sigma (y-x)\\
\frac{dy}{dt} = x (\rho-z)-y\\
\frac{dz}{dt} = xy-\beta z\\
\end{aligned}
$$

with $\sigma = 10, \rho = 28, \beta = \frac{8}{3}$ (the chaotic regime).
- **Integration method:** Forward Euler with step size $dt = 0.005$ across $20$ steps per frame.
- **Initial condition:** $(x_0, y_0, z_0) = (1, 1, 1)$.
Simply put, we use the three equations above to calculate the incremental changes $dx$, $dy$, and $dz$, which we then add to the current coordinates to find the next point on the curve. A list of 4,000 recent coordinates is maintained and connected with line segments to form a continuous visual curve. Essentially, $dt$ acts as our resolution parameter: increasing $dt$ draws the curve faster but makes it look jagged, whereas decreasing $dt$ produces a much smoother curve at the expense of rendering speed.

# Rendering
For each point $(x, y, z)$:
1. **Rotation about the $z$-axis** by an angle $\theta$:
    $$
    \begin{aligned}
    x_r &= x \cos(\theta) - y \sin(\theta)\\
    y_r &= y \cos(\theta) + x \sin(\theta)\\
    \end{aligned}
    $$
2. **$z$-offset adjustment:** Shift $z$ by $z_c = 27$ (the center of the wings) so tilting occurs around the midpoint of the structure:
   $$ z_r = z - z_c $$
3. **Tilt transformation** by an angle $\phi$:
    $$
    \begin{aligned}
    \text{depth} = y_r \cos(\phi) - z_r \sin(\phi)\\
    \end{aligned} = z_r \cos(\phi) + y_r \sin(\phi)\\
    $$
4. **Perspective projection:**
    $$ f = \frac{D}{D + \text{depth}} $$
    where camera distance $D = 120$. The screen coordinates are computed as:
    $$ \text{screen\_pos} = \text{center} + \text{scale} \cdot \text{coordinate} \cdot f $$

### Appearance
- **Hue:** Follows the point's $x$-value.
- **Alpha:** Decreases linearly with the age of the point segment.
- **Visual depth cues:** Lightness, alpha, and line width scale with the perspective factor $f$, rendering nearer segments brighter and thicker.

---

*© 2026 Atharva Chauhan, Vox*

