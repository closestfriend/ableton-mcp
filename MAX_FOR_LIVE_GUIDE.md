# Max for Live Arrangement Helper Guide 🎵

## Quick Start for Max Beginners!

### Step 1: Create Your First M4L Device

1. In Ableton, create a MIDI track
2. In the browser, go to: **Max for Live > Max MIDI Effect**
3. Drag "Max MIDI Effect" to your track
4. Click the **Edit** button (wrench icon) - Max will open!

### Step 2: Understanding the Basics

In Max, you work with "objects" (boxes) connected by "patch cords" (lines):
- **Objects** do things (like get info from Live)
- **Patch cords** pass data between objects
- Double-click empty space to create new objects

### Step 3: Essential Objects for Arrangement Control

Here are the magic objects you need:

```
live.path        - Navigate to parts of Live
live.object      - Control what you navigated to
live.observer    - Watch for changes
```

### Step 4: Simple Arrangement Clip Creator

Let's build it step by step! In your Max patcher:

1. **Create a button** (double-click, type "button", press Enter)
2. **Create these objects below it:**

```
[button]
    |
[live.path "live_set tracks 0"]
    |
[live.object]
```

3. **Connect them** by clicking outlets (bottom) and dragging to inlets (top)

4. **Create a message box** (type 'm' key) with this text:
```
call create_clip 16. 8.
```
(This means: create clip at beat 16, length 8 beats)

5. **Connect the message to live.object's right inlet**

Your patch should look like:
```
[button]
    |
[live.path "live_set tracks 0"]
    |                    [call create_clip 16. 8.]
    |                          /
[live.object]----------------
```

6. **Lock the patch** (Cmd+E or Ctrl+E) and click the button!

### Step 5: Making It Controllable from our MCP

Add OSC receive to get commands from our Python script:

```
[udpreceive 9878]  <- listens for network messages
    |
[route /arrangement]
    |
[route /create_clip]
    |
[unpack f f f]  <- splits: track_index, start_time, length
    |   |   |
   (use these values)
```

### Complete Working Example:

Here's a practical device that creates clips:

```max
------------- OBJECTS TO CREATE -------------

[udpreceive 9878]
    |
[route /arrangement]
    |
[route /create_clip]
    |
[unpack i f f]  -- outputs: track_index, start_time, length
    |
[sprintf live_set tracks %i]
    |
[live.path]
    |
[live.object]
    
Connect the start_time and length from unpack to:
[pak create_clip f f]
    |
[prepend call]
    |
(to live.object's right inlet)
```

### Step 6: Add More Functions!

**Delete Clip:**
```max
[route /delete_clip]
    |
[unpack i i]  -- track_index, clip_index
    |
[sprintf live_set tracks %i arrangement_clips %i]
    |
[live.path]
    |
[t b l]
    |   |
    |   [prepend delete]
    |   |
[live.object]
```

**Move Clip:**
```max
Similar setup, but use:
[prepend call move_clip]
```

### Connecting with our MCP:

In our Python MCP, add UDP sending:

```python
import socket

def send_to_max(message):
    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    sock.sendto(message.encode(), ("127.0.0.1", 9878))

# In our arrangement functions:
def _create_arrangement_clip(self, track_index, start_time, length):
    # Send to Max
    osc_message = f"/arrangement/create_clip {track_index} {start_time} {length}"
    send_to_max(osc_message)
    return {"status": "sent to Max for Live"}
```

### Pro Tips for Max Beginners:

1. **Cmd+M (Mac) / Ctrl+M (Win)** = See available messages for any object
2. **Option-click (Mac) / Alt-click (Win)** = Get help for any object
3. **Lock/Unlock** = Cmd+E to switch between editing and using
4. **Print object** = Great for debugging! Shows messages in Max Console

### Common Max Objects You'll Love:

- `print` - Debug messages
- `metro 1000` - Timer (1000ms = 1 second)
- `counter` - Counts things
- `gate` - Routes messages
- `prepend` - Adds text before data
- `append` - Adds text after data

### Next Steps:

1. Save your device as "ArrangementHelper.amxd"
2. It will appear in your User Library
3. Drop it on the Master track
4. Now our MCP can control arrangement!

### Useful Live Object Model Paths:

```
live_set tracks N arrangement_clips M
live_set tracks N
live_set scenes N
live_set cue_points N
```

Where N and M are numbers (track index, clip index, etc.)

Remember: Max might look intimidating, but you're just connecting boxes! Start simple and build up! 🎉
