# Home Assistant - Tortoise Habitat Automation

A comprehensive Home Assistant configuration for automated tortoise habitat environment control. This system monitors temperature and humidity using dual sensors, automatically controls heating, humidification, ventilation, and lighting to maintain optimal conditions for tortoise health.

## 🐢 Overview

This setup provides automated environmental control for a tortoise habitat with:
- **Dual sensor monitoring** (Matcha & Tiptoe sensors) for redundancy and accuracy
- **Automatic temperature control** with heater management
- **Automatic humidity control** with humidifier management
- **Smart fan control** based on outdoor temperature ranges
- **Scheduled lighting** for proper day/night cycles
- **Safety features** including sensor disagreement detection
- **Comprehensive dashboard** for monitoring and manual control

## 📁 Project Structure

```
Home-Assistant/
├── configuration.yaml          # Main configuration file
├── dashboard.yaml              # Dashboard layout
├── scripts.yaml                # Utility scripts
└── Tort-Control/              # Automation files
    ├── main_control.yaml                    # Main environment control automation
    ├── fan_temperature_control.yaml         # Fan automation based on outdoor temp
    ├── lights_schedule.yaml                 # Lights schedule automation
    ├── humidifier_timer.yaml                # Humidifier countdown timer
    └── safety_sensor_disagreement.yaml      # Safety monitoring automation
```

## 🔧 Configuration Components

### Main Configuration (`configuration.yaml`)

#### Recorder Settings
- **Data retention**: 7 days (matches dashboard view)
- **Auto-purge**: Daily at 3:00 AM
- **Commit interval**: 5 seconds (optimized for performance)
- **Excluded entities**: Automation/script states and frequently updating sensors

#### Input Helpers
The system uses several input helpers for configuration:

**Boolean Toggles:**
- `tortoise_automation_enabled` - Master automation switch
- `lights_schedule_enabled` - Enable/disable lights schedule
- `fan_temperature_control_enabled` - Enable/disable fan automation

**Time Settings:**
- `lights_on_time` - Scheduled lights on time
- `lights_off_time` - Scheduled lights off time

**Temperature Settings:**
- `tortoise_temperature_target` - Target temperature (70-90°F, default: 80°F)
- `temperature_hysteresis_range` - Temperature tolerance range (0.5-5°F)
- `fan_temperature_min` - Minimum outdoor temp for fans (50-70°F, default: 60°F)
- `fan_temperature_max` - Maximum outdoor temp for fans (70-90°F, default: 80°F)
- `outdoor_minimum_temperature` - Minimum safe outdoor temperature (50-75°F)
- `sensor_disagreement_threshold` - Alert threshold for sensor differences (2-15°F, default: 5°F)
- `temperature_change_rate_threshold` - Rate threshold for early heater trigger (0.5-5°F/min, default: 1.0°F/min)

**Humidity Settings:**
- `tortoise_humidity_target` - Target humidity (35-65%, default: 50%)
- `humidity_hysteresis_range` - Humidity tolerance range (2-10%)

**Device Protection:**
- `device_min_cycle_time` - Minimum time between device state changes (1-30 min, default: 5 min)

**Humidifier Timer:**
- `humidifier_timer_hours_remaining` - Countdown timer for humidifier refills (0-12 hours, default: 12)

#### Template Sensors

**Averaged Readings:**
- `sensor.tortoise_average_temperature` - Average of both sensors
- `sensor.tortoise_average_humidity` - Average of both sensors

**Status Sensors:**
- `sensor.tortoise_temperature_status` - Status: "Too Cold", "Cool", "Optimal", "Warm", "Too Hot"
- `sensor.tortoise_humidity_status` - Status: "Too Dry", "Slightly Dry", "Optimal", "Too Humid"
- `sensor.tortoise_system_health` - Overall system health with battery and sensor status

**Runtime Tracking:**
- `sensor.heater_runtime_today` - Minutes heater has been on today
- `sensor.humidifier_runtime_today` - Minutes humidifier has been on today
- `sensor.fans_runtime_today` - Minutes fans have been on today

**Other Sensors:**
- `sensor.humidifier_hours_remaining` - Countdown timer display
- `sensor.tortoise_temperature_change_rate` - Temperature change rate (°F/min)
- `binary_sensor.outdoor_safe_for_tortoises` - Whether outdoor conditions are safe

### Automations

#### Main Control Automation (`Tort-Control/main_control.yaml`)
The core automation that runs every minute to control the habitat environment.

**Features:**
- **Temperature Control**: Automatically turns heater on/off based on average temperature
  - Early trigger: Activates heater early if temperature is dropping rapidly
  - Hysteresis: Prevents rapid cycling with configurable ranges
  - Short-cycling protection: Enforces minimum cycle times

- **Humidity Control**: Automatically controls humidifier
  - Maintains humidity within target range
  - Short-cycling protection

- **Lights Schedule Enforcement**: Ensures lights follow schedule even if manually overridden

**Safety:**
- Only runs when both sensors are available
- Requires automation to be enabled
- Logs all actions for troubleshooting

#### Fan Temperature Control (`Tort-Control/fan_temperature_control.yaml`)
Automatically controls fans based on outdoor temperature range.

**Behavior:**
- Turns fans ON when outdoor temperature is within configured range
- Turns fans OFF when outside the range
- Checks every minute and syncs state
- Requires `fan_temperature_control_enabled` to be on

#### Lights Schedule (`Tort-Control/lights_schedule.yaml`)
Automatically controls lights based on scheduled times.

**Features:**
- Turns lights ON at scheduled time
- Turns lights OFF at scheduled time
- Supports overnight schedules (e.g., 22:00 to 06:00)
- Continuously syncs state every minute
- Can be enabled/disabled via toggle

#### Humidifier Timer (`Tort-Control/humidifier_timer.yaml`)
Countdown timer that tracks humidifier runtime.

**Behavior:**
- Counts down from 12 hours when humidifier is ON
- Pauses when humidifier is OFF
- Resets via script when refilled

#### Safety: Sensor Disagreement (`Tort-Control/safety_sensor_disagreement.yaml`)
Monitors for sensor failures by detecting temperature disagreements.

**Behavior:**
- Alerts when sensors disagree by more than threshold (default: 5°F)
- Requires disagreement to persist for 5 minutes
- Sends notification but does NOT disable automation
- Helps identify sensor battery issues or failures

### Scripts

#### Humidifier Refill Reset (`scripts.yaml`)
- Resets humidifier timer to 12 hours
- Sends notification
- Logs the action

### Dashboard (`dashboard.yaml`)

A comprehensive dashboard organized into sections:

1. **Current Environment** - Temperature and humidity gauges
2. **Quick Controls** - Manual device switches (Heater, Humidifier, Fans, Lights)
3. **Sensor Readings** - Individual sensor data from Matcha and Tiptoe
4. **Humidifier Timer** - Timer gauge and reset button
5. **Settings** - All configurable parameters
6. **System Status** - Health indicators and automation toggles
7. **Historical Charts** - 24-hour graphs for temperature, humidity, and device states

## 🎯 Key Features

### Dual Sensor Redundancy
- Uses two independent sensors (Matcha & Tiptoe) for reliability
- Averages readings for accuracy
- Detects sensor disagreements for safety

### Smart Temperature Control
- Hysteresis bands prevent rapid cycling
- Early trigger system activates heater when temperature drops rapidly
- Short-cycling protection enforces minimum on/off times
- Temperature change rate monitoring

### Intelligent Device Management
- Minimum cycle times protect devices from excessive wear
- State duration tracking for all devices
- Runtime statistics for monitoring usage

### Safety & Monitoring
- Sensor disagreement detection
- System health monitoring
- Battery level tracking
- Outdoor temperature safety checks

### User-Friendly Controls
- All settings adjustable via dashboard
- Individual automation toggles
- Manual override capability
- Comprehensive logging

## 📊 Required Entities

### Sensors
- `sensor.tortoise_ht_sensor_temperature` - Matcha sensor temperature
- `sensor.tortoise_ht_sensor_humidity` - Matcha sensor humidity
- `sensor.tortoise_ht_sensor_battery` - Matcha sensor battery
- `sensor.tiptoe_s_ht_sensor_temperature` - Tiptoe sensor temperature
- `sensor.tiptoe_s_ht_sensor_humidity` - Tiptoe sensor humidity
- `sensor.tiptoe_s_ht_sensor_battery` - Tiptoe sensor battery

### Switches
- `switch.heater` - Heater control
- `switch.humidifier` - Humidifier control
- `switch.fans` - Fan control
- `switch.lights` - Lights control

### Weather
- `weather.forecast_home` - Weather entity for outdoor temperature

## 🚀 Setup Instructions

1. **Copy Configuration Files**
   - Copy all YAML files to your Home Assistant `config/` directory
   - Ensure the `Tort-Control/` directory structure is maintained

2. **Verify Entity Names**
   - Update sensor entity names in `configuration.yaml` if yours differ
   - Update switch entity names if needed
   - Configure your weather entity name

3. **Restart Home Assistant**
   - Restart to load the new configuration
   - Check the logs for any errors

4. **Configure Dashboard**
   - Import `dashboard.yaml` or manually add the cards
   - Adjust the dashboard path/name as needed

5. **Set Initial Values**
   - Configure target temperature and humidity
   - Set lights schedule times
   - Adjust other settings as needed

6. **Enable Automations**
   - Enable `tortoise_automation_enabled` to start automation
   - Enable other automation toggles as desired

## ⚙️ Customization

### Adjusting Temperature Ranges
Edit the `input_number` helpers in `configuration.yaml`:
- `tortoise_temperature_target`: Change default target
- `temperature_hysteresis_range`: Adjust tolerance

### Modifying Automation Behavior
Edit the automation files in `Tort-Control/`:
- Minimum cycle times
- Trigger thresholds
- Logging levels

### Dashboard Customization
Modify `dashboard.yaml` to:
- Add/remove cards
- Change layouts
- Adjust chart time ranges

## 📝 Logging

The system logs to custom loggers:
- `tortoise_environment_control` - Main automation logs
- `tortoise_fan_control` - Fan automation logs
- `tortoise_lights_control` - Lights automation logs

View logs in Home Assistant under **Developer Tools > Logs** or filter by logger name.

## 🔍 Troubleshooting

### Automation Not Running
- Check `input_boolean.tortoise_automation_enabled` is ON
- Verify both sensors are reporting data
- Check automation logs for errors

### Sensors Disagreeing
- Check sensor batteries (should be >20%)
- Verify sensor placement and calibration
- Adjust `sensor_disagreement_threshold` if needed

### Devices Cycling Too Frequently
- Increase `device_min_cycle_time`
- Adjust hysteresis ranges
- Check for sensor issues

### Dashboard Not Showing
- Verify dashboard YAML syntax
- Check entity names match your setup
- Ensure Lovelace is configured correctly

## 📈 Monitoring

Monitor your system health via:
- `sensor.tortoise_system_health` - Overall status
- Dashboard charts - Historical trends
- Runtime sensors - Device usage tracking
- System logs - Detailed automation activity

## 🔒 Safety Notes

- This automation does NOT disable itself on sensor disagreement (only alerts)
- Always monitor system health regularly
- Keep sensor batteries charged
- Verify device states match expectations
- Test manual overrides periodically

## 📄 License

This configuration is provided as-is for personal use. Modify as needed for your specific setup.

---

**Last Updated**: Configuration includes optimizations for performance, safety, and usability. All automations include short-cycling protection and comprehensive logging.
