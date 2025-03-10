# Home Assistant Lovelace Kindle Screensaver

![ci](https://github.com/sibbl/hass-lovelace-kindle-screensaver/workflows/ci/badge.svg)

This tool can be used to display a Lovelace view of your Home Assistant instance on a [jailbroken](https://www.mobileread.com/forums/showthread.php?t=320564) Kindle device. It regularly takes a screenshot which can be polled and used as a screensaver image of the [online screensaver plugin](https://www.mobileread.com/forums/showthread.php?t=236104).

If you're looking for a way to render your own HTML, see my other project [hass-kindle-screensaver](https://github.com/sibbl/hass-kindle-screensaver) which renders a React page and can be adapted to your specific needs.

## Sample image

![Sample image](https://raw.githubusercontent.com/sibbl/hass-lovelace-kindle-screensaver/main/assets/sample.png)

## Features

This tool regularly takes a screenshot of a specific page of your home assistant setup. It converts it into the PNG grayscale format which Kindles can display.

Using my [own Kindle 4 setup guide](https://github.com/sibbl/hass-lovelace-kindle-4) or the [online screensaver extension](https://www.mobileread.com/forums/showthread.php?t=236104) for any jailbroken Kindle, this image can be regularly polled from your device so you can use it as a weather station, a display for next public transport departures etc.

## Usage

You may simple set up the [sibbl/hass-lovelace-kindle-screensaver](https://hub.docker.com/r/sibbl/hass-lovelace-kindle-screensaver) docker container. The container exposes a single port (5000 by default).

You can access the image by doing a simple GET request to e.g. `http://localhost:5000/` to receive the most recent image.

Home Assistant related stuff:

| Env Var                   | Sample value                          | Required | Array?\* | Description                                                                                                                                             |
| ------------------------- | ------------------------------------- | -------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `HA_BASE_URL`             | `https://your-hass-instance.com:8123` | yes      | no       | Base URL of your home assistant instance                                                                                                                |
| `HA_SCREENSHOT_URL`       | `/lovelace/screensaver?kiosk`         | yes      | yes      | Relative URL to take screenshot of (btw, the `?kiosk` parameter hides the nav bar using the [kiosk mode](https://github.com/maykar/kiosk-mode) project) |
| `HA_ACCESS_TOKEN`         | `eyJ0...`                             | yes      | no       | Long-lived access token from Home Assistant, see [official docs](https://developers.home-assistant.io/docs/auth_api/#long-lived-access-token)           |
| `LANGUAGE`                | `en`                                  | no       | no       | Language to set in browser and home assistant                                                                                                           |
| `CRON_JOB`                | `* * * * *`                           | no       | no       | How often to take screenshot                                                                                                                            |
| `RENDERING_TIMEOUT`       | `10000`                               | no       | no       | Timeout of render process, helpful if your HASS instance might be down                                                                                  |
| `RENDERING_DELAY`         | `0`                                   | no       | yes      | how long to wait between navigating to the page and taking the screenshot, in milliseconds                                                              |
| `RENDERING_SCREEN_HEIGHT` | `800`                                 | no       | yes      | Height of your kindle screen resolution                                                                                                                 |
| `RENDERING_SCREEN_WIDTH`  | `600`                                 | no       | yes      | Width of your kindle screen resolution                                                                                                                  |
| `ROTATION`                | `0`                                   | no       | yes      | Rotation of image in degrees, e.g. use 90 or 270 to render in landscape                                                                                 |
| `SCALING`                 | `1`                                   | no       | yes      | Scaling factor, e.g. `1.5` to zoom in or `0.75` to zoom out                                                                                             |
| `GRAYSCALE_DEPTH`         | `8`                                   | no       | yes      | Ggrayscale bit depth your kindle supports                                                                                                               |
| `COLOR_MODE`              | `GrayScale`                           | no       | yes      | ColorMode to use, ex: `GrayScale`, or `TrueColor`.                                                                                                      |
| `DITHER`                  | `false`                               | no       | yes      | Apply a dither to the images.                                                                                                                           |

**\* Array** means that you can set `HA_SCREENSHOT_URL_2`, `HA_SCREENSHOT_URL_3`, ... `HA_SCREENSHOT_URL_n` to render multiple pages within the same instance.
If you use `HA_SCREENSHOT_URL_2`, you can also set `ROTATION_2=180`. If there is no `ROTATION_n` set, then `ROTATION` will be used as a fallback.
You can access these additional images by making GET Requests `http://localhost:5000/2`, `http://localhost:5000/3` etc.

You may also simply use the `docker-compose.yml` file inside this repository, configure everything in there and run `docker-compose up`.

### Advanced configuration

Some advanced variables for local usage which shouldn't be necessary when using Docker:

- `OUTPUT_PATH=./output.png` (destination of rendered image. `OUTPUT_2`, `OUTPUT_3`, ... is also supported)
- `PORT=5000` (port of server, which returns the last image)
- `USE_IMAGE_MAGICK=false` (use ImageMagick instead of GraphicsMagick)
- `UNSAFE_IGNORE_CERTIFICATE_ERRORS=true` (ignore certificate errors of e.g. self-signed certificates at your own risk)
