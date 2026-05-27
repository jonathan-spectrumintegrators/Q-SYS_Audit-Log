# Audit Log

## Description
Tracks and logs changes to monitored controls. Changes are logged periodically to an output pin and optionally to the system log.

## Requirements
Requires Q-SYS Designer version 9.10 or newer.

## Controls
* **Generate Report** (trigger): Trigger to immediately generate a changed controls report (does not reset the counter for the interval report, if enabled)
* **Report to Log** (toggle): Send the report to the system log if true
* **Change Detected** (LED): True when a change was detected since the last reporting interval, false if no change was detected
* **Changed Controls Report** (text): Contents of the most recent changed controls report
* **1..n** (text): Component, control, and property (if applicable) name of a property to monitor

## Design-time Properties
* **`Count`**: The number of possible named controls to monitor (2 to 100, default 5)
* **`Report Interval`**: The number of seconds to wait between reporting control changes (prevents spamming the log with things like knob changes), may be between 0 and 86400 (0 disables the automatic reporting)
* **`Debug Print`** (available if `Show Debug` is `Yes`): Level of runtime debugging messages to print
  * `None`: Do not print debug messages (error messages will still be printed)
  * `Debugging`: Prints additional debugging messages and parameters
  * `Function Calls`: Prints each function name as it is called (and sometimes parameters passed)
  * `All`: Both `Debugging` and `Function Calls`

## Usage
Enter the component, control, and property name of the property you want to monitor in each text box. Spaces are allowed and left in as-is. Each referenced control and property must be in the format `Component.Control.Property` or `Component.Control` for trigger buttons (any additional periods will be considered to be part of the control name).

The property is ignored for triggers, so if you have a control with dots in the name you will need to make sure you add a dummy property name for a trigger or else it will try to use the last part of the control name as the property. State trigger controls are not considered to be trigger controls for this plugin, you should monitor a specific property instead.

Remember, everything is case-sensitive.

### Control Name Examples
* `Gain.gain.Position`
* `Flip-Flop.trigger` (note the lack of a property name for a trigger button)
* `Software_Dante_RX_Software-Dante-RX-1.channel.2.input.gain.Value`
* `Snapshot_Controller.load.1.Boolean` (this is a state trigger, so we monitor a specific property)
* `Parametric_Equalizer.type.1.String`

### Usage Example
![Usage Example](https://raw.githubusercontent.com/jonathan-spectrumintegrators/Q-SYS_Audit-Log/main/example.gif)

### Reporting Interval & Logging
If the Changed Controls Report property is non-zero, then at that interval a report will be logged showing which controls have changed, their current value and the time it was last changed. If the control changed multiple times since the last report, only the most recent change is shown. If no controls have been changed, the report will not be generated.

The report goes to the Changed Controls Report text box as well as to the system log if Report to Log is true. One could use the output pin for the report text to log it to a file or show it on a status page or whatever.

Setting the interval to 86400 will cause any changes for an entire day to accumulate in memory, and at the end of the day they will be logged all at once.

Alternatively, setting the interval to 0 and connecting the generate report button to some other logic like a scheduled event or a tech page will store any changes in memory until the report is generated.

## Details
This plugin adds an `EventHandler` to each control to monitor its state. If the `Type` property is found in the `triggerControls` list, it is considered to be a Trigger-style controls and the `TriggerHandler` function is used for the `EventHandler`; for any other control type, the specified property is used and the `ValueHandler` is assigned to the `EventHandler`.

## Known Issues
* Number of inputs has to be 2 or more because if it's 1 then the control names don't have numbers and I can't be bothered to add logic handling that right now.
* Can only monitor one property of a control (you can't monitor `Gain.gain.Value` and `Gain.gain.Position` at the same time for example); this could be fixed, but I don't feel like it

## License
    Control change audit log
    Copyright (C) 2026 Jonathan Dean - Spectrum Integrators (jonathand@spectrumintegrators.com)

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, either version 3 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License
    along with this program.  If not, see <https://www.gnu.org/licenses/>.

(see the _LICENSE_ file for full license details)
