# 01_urdf_basics

## Notes

**Root link:** Every robot needs one link with no parent joint (usually named `base_link`). Without it, RViz's Robot Model display fails.

**Joints connect the tree:** Two links with no joint between them are two separate trees, not one robot. URDF needs a single connected tree.

**Quoting paths in terminal:** `model:=$(pwd)/file.urdf` breaks if the path has a space. Always quote it: `model:="$(pwd)/file.urdf"`.

**Visual vs collision:** They're separate tags but can reuse the same geometry and origin values, as we did for simple shapes.

**Inertia triangle inequality:** A real inertia tensor must satisfy `ixx + iyy ≥ izz` (and the other two permutations). Break this and the simulator won't display the object correctly.

**rpy is radians**, not degrees. `1.5708` = 90°.

**Origin is local, not global:** A link's `<visual>`, `<collision>`, and `<inertial>` origins are relative to that link's own frame — the point where its parent joint attaches it. Not the world origin.

**Joint origin = parent-to-child transform:** It's the offset between the parent link's frame and the child link's frame, not a point on either link individually.

**RViz's inertia box is symbolic:** Turning on inertia display always draws a red/pink translucent box, no matter the real shape (cylinder, sphere, etc.). It shows mass distribution and principal axes only — not collision geometry, and it doesn't recolor the actual visual mesh.

**Rotation changes the tensor:** If a shape is rotated (like a wheel tilted 90° to lie on its side), its principal axes rotate too. The `ixx`/`iyy`/`izz` values must be recalculated for the new orientation, not just copied from the "standing up" formula:

For solid, uniform-density shapes, with mass $m$:

| Shape | $I_{xx}$ | $I_{yy}$ | $I_{zz}$ |
| --- | --- | --- | --- |
| Box (width $w$, depth $d$, height $h$) | $\frac{m}{12}(d^2+h^2)$ | $\frac{m}{12}(w^2+h^2)$ | $\frac{m}{12}(w^2+d^2)$ |
| Cylinder (radius $r$, length $l$, axis along Z) | $\frac{m}{12}(3r^2+l^2)$ | $\frac{m}{12}(3r^2+l^2)$ | $\frac{1}{2}mr^2$ |
| Sphere (radius $r$) | $\frac{2}{5}mr^2$ | $\frac{2}{5}mr^2$ | $\frac{2}{5}mr^2$ |

Two things to keep in mind when you use these.

For the box, $w$, $d$, $h$ map to your URDF size="w d h" in that same X, Y, Z order — so $I_{xx}$ skips $w$, $I_{yy}$ skips $d$, $I_{zz}$ skips $h$. The pattern is: each axis's value ignores the dimension along that same axis.

For the cylinder, the formula above assumes the cylinder's long axis is Z — the default when you write `<cylinder></cylinder>` with no rotation. Once you rotate it 90° to lie on its side (like our wheels), Z stops being the long axis. You then swap which formula goes where: whichever axis is now the long axis gets $\frac{1}{2}mr^2$, and the other two get $\frac{m}{12}(3r^2+l^2)$.

## QUESTIONS

**How do we choose the inertia matrix numbers?**
Use the standard formula for the shape (box, cylinder, sphere), with a realistic mass. Compute around the link's *local* axes — after any rotation the visual has, not the shape's "default" orientation. For simple shapes aligned with the local frame, off-diagonal terms (`ixy`, `ixz`, `iyz`) are zero. They become non-zero only for asymmetric shapes, or shapes not aligned with the frame's axes.

**What number system does `rgba` use?**
Floats from `0.0` to `1.0` per channel — not 0–255. `1 1 1 1` is opaque white; `0 0 0 1` is opaque black; the last value is alpha (opacity), where `0` is fully transparent.
