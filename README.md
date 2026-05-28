# PX4 Atmosphere Library

A lightweight C++ library for standard atmospheric calculations based on the **US Standard Atmosphere 1976** (first layer, up to 11 km altitude).  
Originally developed for the PX4 autopilot ecosystem, it provides essential functions for converting between altitude, pressure, temperature, and air density.

## Features

- Compute air density from pressure and temperature.
- Compute standard air pressure at a given altitude (ISA model).
- Compute altitude from measured pressure and sea‑level pressure (hypsometric equation).
- Get standard temperature at a given altitude.

## Limitations

- Only models the **troposphere** (first layer) of the US Standard Atmosphere 1976.
- Valid for altitudes **0 – 11 km** above mean sea level.
- Above 11 km the model is not accurate.

## API Reference

All functions are defined in the `atmosphere` namespace.

### `getDensityFromPressureAndTemp`

```cpp
float getDensityFromPressureAndTemp(float pressure_pa, float temperature_celsius);
```

Calculate air density (kg/m³) from ambient pressure and temperature.

**Parameters**  
- `pressure_pa` – Ambient pressure in Pascals.  
- `temperature_celsius` – Ambient temperature in degrees Celsius.

**Returns**  
Air density in kg/m³.

---

### `getPressureFromAltitude`

```cpp
float getPressureFromAltitude(float altitude_m);
```

Calculate standard air pressure (Pa) for a given altitude in the standard atmosphere.

**Parameters**  
- `altitude_m` – Altitude above mean sea level in meters.

**Returns**  
Standard pressure in Pascals.

---

### `getAltitudeFromPressure`

```cpp
float getAltitudeFromPressure(float pressure_pa, float pressure_sealevel_pa);
```

Calculate altitude (m) from measured ambient pressure and local sea‑level pressure using the hypsometric equation.

**Parameters**  
- `pressure_pa` – Measured ambient pressure in Pascals.  
- `pressure_sealevel_pa` – Sea‑level pressure in Pascals (e.g., from QNH).

**Returns**  
Altitude in meters above sea level.

---

### `getStandardTemperatureAtAltitude`

```cpp
float getStandardTemperatureAtAltitude(float altitude_m);
```

Return the standard temperature (ISA) at a given altitude.

**Parameters**  
- `altitude_m` – Altitude above mean sea level in meters.

**Returns**  
Standard temperature in degrees Celsius.

## Constants

The following constants are used internally and can be referenced if needed:

| Constant | Value | Description |
|----------|-------|-------------|
| `kAirDensitySeaLevelStandardAtmos` | 1.225 kg/m³ | Sea level air density (ISA) |
| `kAirDensityStandardAtmos11000Amsl` | 0.3639 kg/m³ | Air density at 11 km (model limit) |
| `kAirGasConstant` | 287.1 J/(kg·K) | Specific gas constant for dry air |
| `kAbsoluteNullCelsius` | -273.15 °C | Absolute zero in Celsius |
| `kTempRefKelvin` | 288.15 K | Sea level temperature in Kelvin |
| `kTempGradient` | -0.0065 K/m | Temperature lapse rate in troposphere |
| `kPressRefSeaLevelPa` | 101325 Pa | Sea level pressure (ISA) |

## Example Usage

```cpp
#include "atmosphere.h"
#include <iostream>

int main() {
    float altitude = 2500.0f; // 2.5 km

    // Standard pressure and temperature at 2500m
    float pressure = atmosphere::getPressureFromAltitude(altitude);
    float temp_std = atmosphere::getStandardTemperatureAtAltitude(altitude);

    std::cout << "At " << altitude << " m:\n";
    std::cout << "  Pressure = " << pressure << " Pa\n";
    std::cout << "  Std Temp = " << temp_std << " °C\n";

    // Compute density using actual (here standard) values
    float rho = atmosphere::getDensityFromPressureAndTemp(pressure, temp_std);
    std::cout << "  Density  = " << rho << " kg/m³\n";

    // Convert pressure back to altitude (using sea level pressure)
    float recovered_alt = atmosphere::getAltitudeFromPressure(pressure, atmosphere::kPressRefSeaLevelPa);
    std::cout << "Recovered altitude = " << recovered_alt << " m\n";

    return 0;
}
```

## Integration

- Copy `atmosphere.h` and `atmosphere.cpp` into your project.
- Ensure C++11 or later (no external dependencies besides `<cmath>` and `<geo/geo.h>` – the latter is only used for `CONSTANTS_ONE_G`; you can replace it with `9.80665f` if `geo.h` is not available).
- Compile with `atmosphere.cpp`.

## License

This library is released under the **BSD 3‑Clause License**. See the header comments in the source files for the full license text.

## References

- U.S. Standard Atmosphere, 1976.
- PX4 Autopilot – [original source](https://github.com/PX4/PX4-Autopilot)
```