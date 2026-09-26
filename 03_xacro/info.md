
# 03_xacro — Notes

**Namespace required:** root `<robot>` tag needs `xmlns:xacro="http://www.ros.org/wiki/xacro"`, or `xacro:` tags aren't recognized.

**Property = named constant:** `<xacro:property name="wheel_radius" value="0.05"/>`, reused anywhere with `${wheel_radius}`.

**`${}` does real math**, not just substitution — e.g. `${reflect * wheel_y_offset}` flips a sign to mirror a part.

**Macro = reusable template:** `<xacro:macro name="wheel" params="prefix reflect"> ... </xacro:macro>` is copy-pasted fresh at each call.

**Unique names inside macros are required:** a macro expands at compile time, once per call. Without `${prefix}` in `${prefix}_wheel_link`, two calls would generate the same link name twice — URDF requires every link/joint name to be unique, so the second copy would conflict with the first.

**Launching a xacro file:** same launch command as plain URDF — `urdf_tutorial`'s launch file expands `.xacro` on the fly.

```bash
ros2 launch urdf_tutorial display.launch.py model:="$(pwd)/diff_drive.xacro"
```

**Expanding xacro to plain URDF (for debugging):**

```bash
xacro diff_drive.xacro > diff_drive_expanded.urdf
```
