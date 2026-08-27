# Telegram OpenCV Automation

A Python automation project for controlling a Telegram anonymous bot/chat with screen-based actions and OpenCV image recognition.

The project opens Telegram Desktop, searches for a configured bot, interacts with bot buttons, sends predefined messages, opens the chat menu, and attempts to close/end the chat. It is designed around desktop UI automation, so it depends on your screen layout, Telegram window state, image templates, and timing.

## What This Project Does

This repository contains two automation approaches:

- `telegram_auto.py` uses fixed screen coordinates with `pyautogui`.
- `withopencvimages/ImageRecognition.py` uses OpenCV template matching to find specific Telegram UI elements from screenshots before clicking them.

The OpenCV-based script is the more flexible approach because it searches for saved UI templates instead of relying only on hard-coded coordinates.

## Main Workflow

The automation follows this general sequence:

1. Start Telegram Desktop from the configured `Telegram.exe` path.
2. Click the Telegram search bar.
3. Paste the configured bot username.
4. Open the bot search result.
5. Click the first and second bot buttons.
6. Find the message input box.
7. Paste and send the first message.
8. Open the menu.
9. Send additional predefined messages.
10. Find and click the end-chat controls to close the chat.

## How OpenCV Is Used

`withopencvimages/ImageRecognition.py` uses screenshots and template matching to locate UI elements on the screen:

1. A template image is loaded from `withopencvimages/images/` with `cv2.imread()`.
2. The current screen is captured with `PIL.ImageGrab.grab()`.
3. The screenshot is converted to OpenCV's BGR format with `cv2.cvtColor()`.
4. `cv2.matchTemplate()` compares the template against the screenshot.
5. `cv2.minMaxLoc()` finds the best match location and confidence score.
6. If the score is greater than or equal to the configured confidence threshold, the script clicks the center of the matched template with `pyautogui`.

The default confidence threshold used by the script is `0.80` for the template-based clicks.

## How Automation Works

The scripts use these desktop automation techniques:

- **Clicking:** `pyautogui.moveTo()` moves the cursor and `pyautogui.click()` clicks a location.
- **Typing text:** messages and bot usernames are copied with `pyperclip.copy()` and pasted with `pyautogui.hotkey("ctrl", "v")`.
- **Sending messages:** `pyautogui.press("enter")` sends the currently pasted message.
- **Closing the chat:** the OpenCV script searches for `end_chat_1.png` and `end_chat_2.png` templates and clicks them in sequence, retrying through the menu when necessary.

Using the clipboard is important because the configured messages include Persian text and emoji.

## Requirements

The project does not include a `requirements.txt` file. Based on the actual Python imports, install these Python packages:

```bash
pip install pyautogui pyperclip opencv-python numpy pillow
```

You also need:

- Python 3.x
- Telegram Desktop installed and logged in
- A graphical desktop session where screenshots, mouse movement, and keyboard input are allowed
- The Telegram window visible and using a UI layout that matches the coordinates/templates configured in the scripts

## Project Structure

```text
.
├── LICENSE
├── README.md
├── get_mouse_position.py
├── telegram_auto.py
└── withopencvimages/
    ├── ImageRecognition.py
    └── images/
        ├── button_1.png
        ├── button_2.png
        ├── end_chat_1.png
        ├── end_chat_2.png
        ├── menu.png
        └── message_box.png
```

### File Overview

| Path | Purpose |
| --- | --- |
| `telegram_auto.py` | Coordinate-based Telegram automation script. |
| `withopencvimages/ImageRecognition.py` | OpenCV template-matching automation script. |
| `withopencvimages/images/` | Template images used to locate Telegram UI elements. |
| `get_mouse_position.py` | Utility script for printing the current mouse coordinates. |
| `LICENSE` | MIT License. |

## Installation and Setup

1. Clone the repository:

   ```bash
   git clone <repository-url>
   cd telegram-opencv-automation
   ```

2. Create and activate a virtual environment:

   ```bash
   python -m venv .venv
   ```

   On Windows:

   ```bash
   .venv\Scripts\activate
   ```

   On macOS/Linux:

   ```bash
   source .venv/bin/activate
   ```

3. Install dependencies:

   ```bash
   pip install pyautogui pyperclip opencv-python numpy pillow
   ```

4. Open the script you want to run and adjust the configuration values near the top of the file.

   At minimum, update:

   ```python
   TELEGRAM_EXE = r"D:\AMsh\8.12.2025files\tportable-x64.5.16.6\Telegram\Telegram.exe"
   BOT_USERNAME = "@TeleChateBot"
   MESSAGE = "سلام خوبی."
   ```

5. Make sure Telegram Desktop is logged in and can be opened from the configured executable path.

## How to Run

### OpenCV Template-Matching Version

From the repository root:

```bash
python withopencvimages/ImageRecognition.py
```

This version checks for the required image templates in `withopencvimages/images/`, opens Telegram, performs the bot/chat workflow, and uses OpenCV to find buttons and menu items on the screen.

### Coordinate-Based Version

From the repository root:

```bash
python telegram_auto.py
```

This version clicks fixed screen coordinates configured in `telegram_auto.py`.

### Mouse Position Helper

Use this helper to find screen coordinates for the coordinate-based script:

```bash
python get_mouse_position.py
```

Move the mouse to the desired UI element and note the printed `X` and `Y` values. Press `CTRL+C` to stop.

## Configuration and Customization

The scripts are configured directly with constants in the Python files.

Common values to customize:

- `TELEGRAM_EXE`: path to `Telegram.exe`.
- `BOT_USERNAME`: Telegram bot username to search for.
- `MESSAGE`: first message sent to the chat.
- `MESSAGES`: list of additional messages.
- Wait values such as `TELEGRAM_START_WAIT`, `SEARCH_WAIT`, `BOT_WAIT`, `BUTTON_WAIT`, `MESSAGE_WAIT`, and `END_CHAT_WAIT`.
- Coordinate values in `telegram_auto.py`, such as `SEARCH_X`, `SEARCH_Y`, `BUTTON_1_X`, `BUTTON_1_Y`, and message/menu/end-chat positions.
- OpenCV matching settings in `withopencvimages/ImageRecognition.py`, especially `confidence`, `retries`, and `retry_delay` values passed to `find_and_click()`.

Because this is screen automation, you may need to adjust values when changing monitor resolution, display scaling, Telegram theme, Telegram language, font size, or window size.

## Image Template Files

The OpenCV automation script expects these template files inside `withopencvimages/images/`:

| Template | Used For |
| --- | --- |
| `button_1.png` | Finding and clicking the first bot button. |
| `button_2.png` | Finding and clicking the second bot button. |
| `message_box.png` | Finding the message input area. |
| `menu.png` | Opening the Telegram chat/menu area. |
| `end_chat_1.png` | Finding the first end-chat option. |
| `end_chat_2.png` | Finding the final end-chat confirmation/control. |

If Telegram's UI appearance changes, these templates may stop matching. Replace them with fresh screenshots cropped tightly around the target UI element.

For best results:

- Keep templates small and focused on the target button/control.
- Capture templates at the same display scaling and Telegram theme used during automation.
- Avoid including large background areas that may change.
- Recreate templates after changing resolution, DPI scaling, language, or theme.

## Example Workflow

A typical OpenCV-based run looks like this:

```text
Start script
└── Check required image templates
    └── Open Telegram Desktop
        └── Search for configured bot username
            └── Open bot result
                └── Find and click button_1.png
                    └── Find and click button_2.png
                        └── Find message_box.png
                            └── Paste and send MESSAGE
                                └── Find menu.png
                                    └── Send each item in MESSAGES
                                        └── Find end_chat_1.png
                                            └── Find end_chat_2.png
                                                └── Finish
```

## Troubleshooting

### Telegram does not open

- Confirm `TELEGRAM_EXE` points to the correct `Telegram.exe` location.
- Make sure Telegram Desktop is installed and accessible.
- Run the script from a normal desktop session, not a headless terminal.

### The bot is not found or opened

- Check that `BOT_USERNAME` is correct.
- Verify that Telegram search is reachable at the configured search position.
- Increase `SEARCH_WAIT` or `BOT_WAIT` if Telegram is slow.

### OpenCV cannot find a button or menu item

- Recreate the matching template image in `withopencvimages/images/`.
- Make sure Telegram uses the same theme, language, scale, and window size as when the template was captured.
- Lowering the confidence threshold may make matching more tolerant, but can also cause incorrect clicks.
- Ensure the target UI element is visible on screen before the script tries to find it.

### Clicks happen in the wrong place

- For `telegram_auto.py`, update the hard-coded coordinates.
- Use `python get_mouse_position.py` to measure current screen positions.
- Check display scaling and multi-monitor layout.

### Text is not typed correctly

- The scripts paste text through the clipboard with `pyperclip`.
- Make sure clipboard access is available in your desktop session.
- Click the message input manually once to verify Telegram accepts pasted text.

### Screenshot capture fails

- The OpenCV version uses `PIL.ImageGrab.grab()`.
- Run the script in a graphical desktop environment.
- Check operating system privacy/security permissions for screen recording or screenshot access if required.

## Limitations

- The automation is sensitive to screen resolution, display scaling, Telegram theme, UI language, and window placement.
- The project currently stores configuration directly in Python files rather than a separate config file.
- There is no packaged CLI or `requirements.txt` in the repository.
- The scripts require an active desktop session and are not designed for headless execution.
- UI changes in Telegram or the target bot can break coordinates or image templates.
- The automation does not verify message delivery beyond pressing Enter.

## Responsible Use Disclaimer

This repository is intended for educational and automation experimentation with Python, OpenCV, and desktop UI control. Use it responsibly, follow Telegram's terms and applicable laws, and do not use automation to spam, harass, or contact people without permission.

## License

This project is licensed under the MIT License. See [`LICENSE`](LICENSE) for details.
