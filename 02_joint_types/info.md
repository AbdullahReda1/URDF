
# 02_joint_types — Notes

**Six joint types:** fixed (0 DOF), continuous (spins forever), revolute (spins within limits), prismatic (slides within limits), planar (2 slide + 1 rotate, in one plane), floating (6 DOF, unconstrained).

**DOF ≠ joint count:** a robot's total degrees of freedom is the sum of each joint's own DOF, not the number of joints. A fixed joint adds 0.

**`axis` means different things per type:** slide direction for prismatic, spin direction for revolute/continuous, and for planar it's the *normal* of the plane (the direction the plane faces), not a slide/spin direction.

**Planar and floating aren't 1-DOF, so the JSP GUI skips them** — it only makes sliders for single-DOF joints. Real motion for these normally comes from a physics engine or a controller script.

**Building planar/floating by hand (chained-joint trick):** since one joint connects only two links, multi-DOF motion is built by chaining simple 1-DOF joints (prismatic/revolute) through small dummy links in between. Planar = 3 chained joints (X, Y, yaw). Floating = 6 chained joints (X, Y, Z, roll, pitch, yaw).

**Dummy links need `<inertial>` but no `<visual>`/`<collision>`:** give them a tiny mass and tiny inertia so the physics engine doesn't treat them as broken, without affecting the real robot's dynamics.

**Chain order matters:** translate first, then rotate last, means rotation sliders spin the part in place after it has already moved. Reversing the order changes what "forward" means for the later translations.
