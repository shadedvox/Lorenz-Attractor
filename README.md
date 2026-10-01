# Lorenz Attractor
A single-line 3D lorenz attractor rendered on a 2D canvas. A lorenz attractor is a 3D shap resembling a butterfly that visualizes the chaotic, non-repeating path of a simplified weather and fluid-flow system over time

# Usage
Open the `main.html` file in a browser, or paste the `data:text/html,...` one-liner from `mini.html` into the address bar.

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
