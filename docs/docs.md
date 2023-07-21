# PyRolL Linear Thermal Extension Plugin Documentation

# Model Approach

According to the approach of linear thermal expansion, a length dimension of a body changes like the following equation
in dependence of its temperature,
where $T_0$ is the base temperature and $l_0$ the length at this temperature. $\alpha$ is the thermal expansion
coefficient and $T_1$ and $l_1$ are temperature and length after heating or cooling.

$$
l_1 = l_0 \left[1 + \alpha \left( T_1 - T_0 \right)\right]
$$

# Implementation

For transports, the length of the out profile is scaled according to the equation above.
Also, the cross-section is scaled equiaxially with the same factor.

For roll passes, since the geometry is constrained in the cross-section plane by the rolls and spreading behavior, all
thermal expansion is considered to go into length of the workpiece.
Therefore, the length is scaled by thrice the usual length expansion factor.

The expansion coefficient $\alpha$ is defined as hook `Profile.thermal_expansion_coefficient`.
A default implementation for general steel materials is provided with value $15.0 \cdot 10^{-6} \mathrm{K^{-1}}$, which reacts on the key `"steel"` in `Profile.material`.
Give your own value as constant parameter on profile creation or by an hook implementation.

Non-linear expansion behavior can be mimicked by implementing a hook function like below in dependence of the profile temperature.
This enables to use a mean linear expansion coefficient in dependence of the regarded temperature range within one unit.
Calculation can be done for example by interpolation tabular data.

```python
from pyroll.core import Unit

@Unit.Profile.thermal_expansion_coefficient
def temperature_dependent_thermal_expansion_coefficient(self: Unit.Profile):
    in_temperature = self.unit.in_profile.temperature
    out_temperature = self.unit.out_profile.temperature
    
    alpha = ... # calculate mean thermal expansion coefficient in dependence on above temperatures
    
    return alpha
```