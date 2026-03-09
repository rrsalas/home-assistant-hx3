# Hx 3 Thermostat

[![GitHub Release](https://img.shields.io/github/release/rrsalas/home-assistant-hx3.svg?style=for-the-badge)](https://github.com/rrsalas/home-assistant-hx3/releases)
[![License](https://img.shields.io/github/license/rrsalas/home-assistant-hx3.svg?style=for-the-badge)](LICENSE)
[![hacs](https://img.shields.io/badge/HACS-Custom-orange.svg?style=for-the-badge)](https://github.com/custom-components/hacs)

A Home Assistant custom integration for [Johnson Controls Hx 3 thermostats](https://devauthor.jci.com/residential-equipment/residential-thermostats/hx3_touch_screen_thermostat_ds).

![Hx 3 Thermostat](hx3.png)

---

## Important Notes

- This is a fork of [jaredhobbs/home-assistant-hx3](https://github.com/jaredhobbs/home-assistant-hx3) with the following fixes:
  - All required Python dependencies (`gql`, `graphql-core`, etc.) are now **vendored inside the integration** so Home Assistant does not need to install them externally.
  - Updated API endpoint from `hx.kraftful.cloud` to `hx-thermostat.herokuapp.com`.
- The share code from the Hx app is **only valid for 7 days**. If setup fails, generate a new one.

---

## Prerequisites

Before installing, make sure you have the **Hx mobile app** installed and your thermostat is already set up and working in the app.

---

## Step 1 — Get Your Share Code

You will need an email address and a share code from the Hx mobile app.

**iOS:** Open the Hx app → Settings → Multiple Users → tap **Share Code**

**Android:** Open the Hx app → Settings → Manage Account → Share Account → tap **Share Code**

The app will generate a message containing a link like this:

```
https://hx.kraftful.app/signIn/<your-email>/<your-token>
```

Note down your **email address** and the **token** (the long string at the end of the URL). You will need both during setup.

---

## Step 2 — Install via HACS

1. In Home Assistant, open **HACS → Integrations**
2. Click the **⋮ menu** (top right) → **Custom Repositories**
3. Enter the repository URL:
   ```
   https://github.com/rrsalas/home-assistant-hx3
   ```
   and select **Integration** as the category
4. Click **Add**
5. Find **Hx 3 Thermostat** in the list and click **Download**
6. **Restart Home Assistant** when prompted

---

## Step 3 — Configure the Integration

> ⚠️ This step is required and easy to miss — the integration will not work without it.

1. Go to **Settings → Devices & Services**
2. Click **+ Add Integration** (bottom right)
3. Search for **Hx 3 Thermostat** and select it
4. Enter your credentials:
   - **Email:** your email address from the share link
   - **Hx code:** the token from the share link
5. Click **Submit**

Your thermostat will appear as a climate entity in Home Assistant.

---

## Manual Installation (without HACS)

1. Download or clone this repository
2. Copy the `custom_components/hx3` folder into your HA config directory at `/config/custom_components/hx3`
3. Restart Home Assistant
4. Follow Step 3 above to configure

---

## Supported Features

- Current temperature and humidity
- Target temperature (heat, cool, heat/cool modes)
- HVAC mode control (off, heat, cool, heat/cool)
- Fan mode control (auto, on)
- Away/preset mode
- Outdoor temperature display
- Auxiliary heat (if supported by your unit)

---

## Tips for US Users

### Set Temperature to Fahrenheit
If your temperatures are showing in Celsius, your HA unit system needs to be set to US customary. Add or update this in your `configuration.yaml`:

```yaml
homeassistant:
  unit_system: us_customary
```

If you see a message in Settings → System → General saying you cannot change units in the UI, this is why — it must be set in `configuration.yaml`.

### Outdoor Temperature & Humidity Sensors
The integration exposes outdoor temperature and humidity as **attributes** on the climate entity, not as standalone sensors. To use them on dashboards or in automations, add template sensors to your `configuration.yaml`:

```yaml
template:
  - sensor:
      - name: "First Floor Outdoor Temperature"
        unit_of_measurement: "°F"
        state: "{{ state_attr('climate.first_floor', 'outdoor_temperature') }}"
        device_class: temperature

      - name: "First Floor Humidity"
        unit_of_measurement: "%"
        state: "{{ (state_attr('climate.first_floor', 'current_humidity') * 100) | round(0) }}"
        device_class: humidity
```

> ⚠️ Note the `* 100` in the humidity template — the API returns humidity as a decimal (e.g. `0.51`) so it must be multiplied to get the correct percentage (`51%`). Repeat for each thermostat, replacing `climate.first_floor` with your entity ID.

---

## Troubleshooting

**Integration doesn't appear after install**
Make sure you restarted Home Assistant after installing. Then go to Settings → Devices & Services → + Add Integration and search for "Hx 3 Thermostat".

**Authentication fails**
Your share code may have expired — it is only valid for 7 days. Generate a new one from the Hx app and try again.

**Thermostat shows no entities**
Delete the integration from Settings → Devices & Services and re-add it following Step 3 above. Make sure you are entering fresh credentials.

**Connection errors**
Verify the Hx app on your phone is still working and connected to your thermostat. If the app works but HA doesn't, check your Home Assistant logs at Settings → System → Logs for `hx3` errors.

---

## Credits

- Original integration by [@jaredhobbs](https://github.com/jaredhobbs)
- API endpoint update and dependency fixes by [@dpenney](https://github.com/dpenney)
- Vendored dependencies and installation fixes by [@rrsalas](https://github.com/rrsalas)

---

## Contributions Welcome

If you find a bug or want to improve this integration, please open an issue or pull request at [github.com/rrsalas/home-assistant-hx3](https://github.com/rrsalas/home-assistant-hx3).