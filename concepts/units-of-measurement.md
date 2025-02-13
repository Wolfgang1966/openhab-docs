---
layout: documentation
title: Units Of Measurement
---

# Units Of Measurement

To express measured values in a scientific correct unit the framework supports units of measurement.
By using quantified decimal values in state updates and commands, the framework is able to automatically convert values to a desired unit which may be defined by the system locale or on a per-use-basis.

## QuantityType

Bindings use the `QuantityType` to post updates of sensor data with a quantifying unit.
This way the framework and/or the user is able to convert the quantified value to other matching units:

A weather binding which reads temperature values in °C would use the `QuantityType` to indicate the unit as °C.
The framework is then able to convert the values to either °F or Kelvin according to the configuration of the system.
The default conversion the framework will use is locale-based:
The framework tries to convert a `QuantityType` to the default unit of the configured locale.
This is the imperial system for the United States (locale US) and Liberia (language tag "en-LR").
The metric system with SI units is used for the rest of the world.
This conversion will convert the given `QuantityType` into a default unit for the specific dimension of the type.
This is:

| Dimension     | default unit metric        | default unit imperial  |
|---------------|----------------------------|------------------------|
| Length        | Meter (m)                  | Inch (in)              |
| Temperature   | Celsius (°C)               | Fahrenheit (°F)        |
| Pressure      | Hectopascal (hPa)          | Inch of mercury (inHg) |
| Speed         | Kilometers per hour (km/h) | Miles per hour (mph)   |
| Intensity     | Irradiance (W/m2)          | Irradiance (W/m2)      |
| Dimensionless | Abstract unit one (one)    | Abstract unit one (one)|
| Angle         | Degree (°)                 | Degree (°)             |

## NumberItem linked to QuantityType Channel

In addition to the automated conversion the `NumberItem` linked to a Channel delivering `QuantityTypes` can be configured to always have state updates converted to a specific unit.
The unit given in the state description is parsed and then used for conversion (if necessary).
The framework assumes that the unit to parse is always the last token in the state description.
If the parsing failed the locale-based default conversion takes place.

`Number:Temperature temperature "Outside [%.2f °F]" { channel="...:current#temperature" }`

In the example the `NumberItem` is specified to bind to Channels which offer values from the dimension `Temperature`.
Without the dimension information the `NumberItem` only will receive updates of type `DecimalType` without a unit and any conversion.
The state description defines two decimal places for the value and the fix unit °F.
In case the state description should display the unit the binding delivers or the framework calculates through locale-based conversion the pattern will look like this:

`"Outside [%.2f %unit%]"`

The special placeholder `%unit%` will then be replaced by the actual unit symbol.
The placeholder `%unit%` can be placed anywhere in the state description.

### Defining ChannelTypes

In order to match `NumberItems` and Channels and define a default state description with unit placeholder the Channel also has to provide an Item type which includes the dimension information:

```xml
<channel-type id="temperature">
    <item-type>Number:Temperature</item-type>
    <label>Temperature</label>
    <description>Current temperature</description>
    <state readOnly="true" pattern="%.1f %unit%" />
</channel-type>
```

The state description pattern "%.1f %unit%" describes the value format as floating point with one decimal place and also the special placeholder for the unit.

## Implementing UoM

When creating QuantityType states the framework offers some useful packages and classes:
The `org.openhab.core.library.unit` package contains the classes `SIUnits`, `ImperialUnits` and `Units` which provide units unique to either of the measurement systems and common units used in both systems.

The `MetricPrefix` class provides prefixes like MILLI, CENTI, HECTO, etc. and the `BinaryPrefix` class provides prefixes like KIBI, MEBI, etc. both of which are wrappers to create derived units.

The `org.openhab.core.library.dimension` and `javax.measure.quantity` packages provide interfaces which are used to type the generic QuantityType and units.

## List of Units

All units which are currently supported by default are listed in the tables below.

Imperial (base unit symbols):

| Type               | Unit                   | Symbol  |
|--------------------|------------------------|---------|
| Length             | Inch                   | in      |
| Length             | Foot                   | ft      |
| Length             | Yard                   | yd      |
| Length             | Chain                  | ch      |
| Length             | Furlong                | fur     |
| Length             | Mile                   | mi      |
| Length             | League                 | lea     |
| Pressure           | Inch of Mercury        | inHg    |
| Pressure           | Pound per square inch  | psi     |
| Speed              | Miles per Hour         | mph     |
| Temperature        | Fahrenheit             | °F      |
| Volume             | Gallon (US)            | gal     |
| VolumetricFlowRate | Gallon (US) per minute | gal/min |

SI (base unit symbols):

| Type                   | Unit                             | Symbol |
|------------------------|----------------------------------|--------|
| Acceleration           | Metre per Second squared         | m/s²   |
| Acceleration           | Standard Gravity                 | ɡₙ     |
| AmountOfSubstance      | Mole                             | mol    |
| AmountOfSubstance      | Deutscher Härtegrad              | °dH    |
| Angle                  | Radian                           | rad    |
| Angle                  | Degree                           | °      |
| Angle                  | Minute Angle                     | '      |
| Angle                  | Second Angle                     | ''     |
| Area                   | Square Metre                     | m²     |
| ArealDensity           | Dobson Unit                      | DU     |
| CatalyticActivity      | Katal                            | kat    |
| DataAmount             | Bit                              | bit    |
| DataAmount             | Byte                             | B      |
| DataAmount             | Octet                            | o      |
| DataTransferRate       | Bit per Second                   | bit/s  |
| Density                | Gram per cubic Metre             | g/m³   |
| Dimensionless          | Percent                          | %      |
| Dimensionless          | Parts per Million                | ppm    |
| Dimensionless          | Decibel                          | dB     |
| ElectricPotential      | Volt                             | V      |
| ElectricCapacitance    | Farad                            | F      |
| ElectricCharge         | Coulomb                          | C      |
| ElectricCharge         | Ampere Hour                      | Ah     |
| ElectricConductance    | Siemens                          | S      |
| ElectricConductivity   | Siemens per Metre                | S/m    |
| ElectricCurrent        | Ampere                           | A      |
| ElectricInductance     | Henry                            | H      |
| ElectricResistance     | Ohm                              | Ω      |
| Energy                 | Joule                            | J      |
| Energy                 | Watt Second                      | Ws     |
| Energy                 | Watt Hour                        | Wh     |
| Energy                 | Volt-Ampere Hour                 | VAh    |
| Energy                 | Volt-Ampere Reactive Hour        | varh   |
| Force                  | Newton                           | N      |
| Frequency              | Hertz                            | Hz     |
| Illuminance            | Lux                              | lx     |
| Intensity              | Irradiance                       | W/m²   |
| Intensity              | Microwatt per square Centimeter  | µW/cm² |
| Length                 | Metre                            | m      |
| LuminousFlux           | Lumen                            | lm     |
| LuminousIntensity      | Candela                          | cd     |
| MagneticFlux           | Weber                            | Wb     |
| MagneticFluxDensity    | Tesla                            | T      |
| Mass                   | Gram                             | g      |
| Power                  | Watt                             | W      |
| Power                  | Volt-Ampere                      | VA     |
| Power                  | Volt-Ampere Reactive             | var    |
| Power                  | Decibel-Milliwatts               | dBm    |
| Pressure               | Pascal                           | Pa     |
| Pressure               | Hectopascal                      | hPa    |
| Pressure               | Millimetre of Mercury            | mmHg   |
| Pressure               | Bar                              | bar    |
| Radioactivity          | Becquerel                        | Bq     |
| RadiationDoseAbsorbed  | Gray                             | Gy     |
| RadiationDoseEffective | Sievert                          | Sv     |
| SolidAngle             | Steradian                        | sr     |
| Speed                  | Metre per Second                 | m/s    |
| Speed                  | Knot                             | kn     |
| Temperature            | Kelvin                           | K      |
| Temperature            | Celsius                          | °C     |
| Time                   | Second                           | s      |
| Time                   | Minute                           | min    |
| Time                   | Hour                             | h      |
| Time                   | Day                              | d      |
| Time                   | Week                             | week   |
| Time                   | Year                             | y      |
| Volume                 | Litre                            | l      |
| Volume                 | Cubic Metre                      | m³     |
| VolumetricFlowRate     | Litre per Minute                 | l/min  |
| VolumetricFlowRate     | Cubic Metre per Second           | m³/s   |
| VolumetricFlowRate     | Cubic Metre per Minute           | m³/min |
| VolumetricFlowRate     | Cubic Metre per Hour             | m³/h   |
| VolumetricFlowRate     | Cubic Metre per Day              | m³/d   |

Metric Prefixes:

| Name  | Symbol | Factor |
|-------|--------|--------|
| Yotta | Y      | 10²⁴   |
| Zetta | Z      | 10²¹   |
| Exa   | E      | 10¹⁸   |
| Peta  | P      | 10¹⁵   |
| Tera  | T      | 10¹²   |
| Giga  | G      | 10⁹    |
| Mega  | M      | 10⁶    |
| Kilo  | k      | 10³    |
| Hecto | h      | 10²    |
| Deca  | da     | 10     |
| Deci  | d      | 10⁻¹   |
| Centi | c      | 10⁻²   |
| Milli | m      | 10⁻³   |
| Micro | µ      | 10⁻⁶   |
| Nano  | n      | 10⁻⁹   |
| Pico  | p      | 10⁻¹²  |
| Femto | f      | 10⁻¹⁵  |
| Atto  | a      | 10⁻¹⁸  |
| Zepto | z      | 10⁻²¹  |
| Yocto | y      | 10⁻²⁴  |

Binary Prefixes:

| Name | Symbol | Factor |
| ---- | ------ | ------ |
| Kibi | Ki     | 2¹⁰    |
| Mebi | Mi     | 2²⁰    |
| Gibi | Gi     | 2³⁰    |
| Tebi | Ti     | 2⁴⁰    |
| Pebi | Pi     | 2⁵⁰    |
| Exbi | Ei     | 2⁶⁰    |
| Zebi | Zi     | 2⁷⁰    |
| Yobi | Yi     | 2⁸⁰    |

To use the prefixes simply add the prefix to the unit symbol - for example:

- milliAmpere - `mA`
- centiMetre - `cm`
- kiloWatt - `kW`
- KibiByte - `KiB`
