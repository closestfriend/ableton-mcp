# Ableton MCP - New Features Guide 🎵

## Arrangement View Integration

### Getting Arrangement Info
```python
# Get current arrangement state
result = get_arrangement_info()
# Returns: playing_position, loop_start, loop_length, song_length, etc.
```

### Navigation
```python
# Jump to specific time (in beats)
set_arrangement_position(time=32.0)

# Get all tracks with their arrangement clips
tracks = get_arrangement_tracks()
```

### Clip Operations
```python
# Create a new clip at beat 16 with 8 beats length
create_arrangement_clip(track_index=0, start_time=16.0, length=8.0)

# Move a clip to a new position
move_arrangement_clip(track_index=0, clip_index=0, new_start_time=24.0)

# Resize a clip
resize_arrangement_clip(track_index=0, clip_index=0, new_start=16.0, new_end=32.0)

# Delete a clip
delete_arrangement_clip(track_index=0, clip_index=0)
```

### Selection & Editing
```python
# Select a region (beats 16-32 on all tracks)
select_arrangement_region(start_time=16.0, end_time=32.0)

# Copy the selected region
copy_arrangement_region()

# Paste at beat 64
paste_arrangement_region(time=64.0)

# Duplicate selected region
duplicate_arrangement_region()
```

### Locators/Markers
```python
# Add a marker at beat 32
add_locator(time=32.0, name="Chorus")

# Get all locators
locators = get_locators()

# Jump to a locator
jump_to_locator(index=0)
```

## VST/AU Plugin Support

### Discovering Plugins
```python
# Get all available plugins
plugins = get_available_plugins(plugin_type="all")

# Get only VST plugins
vst_plugins = get_available_plugins(plugin_type="vst")

# Get only AU plugins (macOS)
au_plugins = get_available_plugins(plugin_type="au")
```

### Loading Plugins
```python
# Load a specific VST plugin
load_vst_plugin(track_index=0, plugin_name="Serum", plugin_type="vst")

# Auto-detect plugin type
load_vst_plugin(track_index=1, plugin_name="Kontakt 7", plugin_type="auto")
```

### Controlling Plugin Parameters
```python
# Get all parameters for a plugin
params = get_plugin_parameters(track_index=0, device_index=1)

# Set a parameter value (0.0 to 1.0)
set_plugin_parameter(
    track_index=0, 
    device_index=1, 
    parameter_index=5, 
    value=0.75
)
```

## Important Notes

1. **Arrangement View Limitations**: Some arrangement operations (like copy/paste) have limited API access in the Ableton Remote Script framework. The implementations use workarounds where possible.

2. **Plugin Compatibility**: The VST/AU loading depends on what's available in your Ableton browser. Make sure your plugins are properly scanned in Ableton's preferences.

3. **Testing**: Test these features in your development environment before deploying to production!

## Example: Creating a Song Structure
```python
# Set tempo
set_tempo(128)

# Create tracks
create_midi_track(index=-1)
set_track_name(track_index=0, name="Drums")

# Load a drum kit
load_vst_plugin(track_index=0, plugin_name="Drum Rack")

# Create intro clip
create_arrangement_clip(track_index=0, start_time=0, length=16)

# Add markers
add_locator(time=0, name="Intro")
add_locator(time=16, name="Verse 1")
add_locator(time=48, name="Chorus")

# Navigate to chorus
jump_to_locator(index=2)
```

Happy music making! 🎹✨
