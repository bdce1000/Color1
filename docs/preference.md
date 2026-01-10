# Preference

ColorPickerView supports automatic state persistence using `ColorPickerPreferenceManager`. This allows you to save and restore the selected color, selector position, and slider positions.

## Automatic State Management

### Setting Preference Name

Enable state persistence by setting a preference name:

```kotlin
colorPickerView.setPreferenceName("MyColorPicker")
```

Or via XML:

```xml
app:preferenceName="MyColorPicker"
```

### Lifecycle-based Saving

Set a lifecycle owner to automatically save states when the activity/fragment is destroyed:

```kotlin
colorPickerView.setLifecycleOwner(this)
```

When the lifecycle owner is destroyed, all states (color, selector position, slider positions) are automatically saved.

## Manual State Management

### Saving States

Save the current state manually:

```kotlin
ColorPickerPreferenceManager.getInstance(this)
    .saveColorPickerData(colorPickerView)
```

### Restoring States

Restore previously saved states:

```kotlin
ColorPickerPreferenceManager.getInstance(this)
    .restoreColorPickerData(colorPickerView)
```

## ColorPickerPreferenceManager

`ColorPickerPreferenceManager` provides fine-grained control over saved data.

### Get Instance

```kotlin
val manager = ColorPickerPreferenceManager.getInstance(context)
```

### Custom SharedPreferences Name

Use a custom SharedPreferences file name (useful for compatibility with other preference systems):

```kotlin
// Create with custom preference name
val manager = ColorPickerPreferenceManager.getInstance(context, "my_custom_prefs")

// Or change the preference name after creation
manager.setSharedPreferenceName(context, "my_custom_prefs")
```

### Access SharedPreferences Directly

```kotlin
val sharedPreferences = manager.sharedPreferences
```

## Managing Individual Values

### Color

```kotlin
// Save color
manager.setColor("MyColorPicker", Color.RED)

// Get saved color (with default value)
val color = manager.getColor("MyColorPicker", Color.WHITE)

// Clear saved color
manager.clearSavedColor("MyColorPicker")
```

### Selector Position

```kotlin
// Save selector position
manager.setSelectorPosition("MyColorPicker", Point(120, 120))

// Get saved position (with default)
val position = manager.getSelectorPosition("MyColorPicker", Point(0, 0))

// Clear saved position
manager.clearSavedSelectorPosition("MyColorPicker")
```

### Alpha Slider Position

```kotlin
// Save alpha slider position
manager.setAlphaSliderPosition("MyColorPicker", 150)

// Get saved position
val position = manager.getAlphaSliderPosition("MyColorPicker", 0)

// Clear saved position
manager.clearSavedAlphaSliderPosition("MyColorPicker")
```

### Brightness Slider Position

```kotlin
// Save brightness slider position
manager.setBrightnessSliderPosition("MyColorPicker", 200)

// Get saved position
val position = manager.getBrightnessSliderPosition("MyColorPicker", 0)

// Clear saved position
manager.clearSavedBrightnessSlider("MyColorPicker")
```

### Clear All Data

```kotlin
manager.clearSavedAllData()
```

## Complete Example

```kotlin
class MainActivity : AppCompatActivity() {

    private lateinit var colorPickerView: ColorPickerView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        colorPickerView = findViewById(R.id.colorPickerView)
        val alphaSlideBar = findViewById<AlphaSlideBar>(R.id.alphaSlideBar)
        val brightnessSlideBar = findViewById<BrightnessSlideBar>(R.id.brightnessSlideBar)

        // Attach sliders
        colorPickerView.attachAlphaSlider(alphaSlideBar)
        colorPickerView.attachBrightnessSlider(brightnessSlideBar)

        // Enable automatic state persistence
        colorPickerView.setPreferenceName("MainColorPicker")
        colorPickerView.setLifecycleOwner(this)

        colorPickerView.setColorListener(ColorEnvelopeListener { envelope, fromUser ->
            updateUI(envelope)
        })
    }

    private fun updateUI(envelope: ColorEnvelope) {
        // Update your UI
    }
}
```

## Multiple Color Pickers

When using multiple color pickers, give each one a unique preference name:

```kotlin
// First color picker
colorPickerView1.setPreferenceName("BackgroundColorPicker")
colorPickerView1.setLifecycleOwner(this)

// Second color picker
colorPickerView2.setPreferenceName("TextColorPicker")
colorPickerView2.setLifecycleOwner(this)
```

## Initial Color with Preferences

When you set both an initial color and a preference name:

```kotlin
colorPickerView.setPreferenceName("MyColorPicker")
colorPickerView.setInitialColor(Color.BLUE)
```

The initial color is only applied on the first launch. After that, the saved preference value takes precedence.

## Tips

!!! tip "Preference Names"

    Use descriptive, unique preference names to avoid conflicts when using multiple color pickers in your app.

!!! warning "Lifecycle Owner"

    Always set the lifecycle owner when using preference names to ensure states are properly saved when the user leaves the screen.

!!! note "State Restoration Timing"

    States are automatically restored when you call `setPreferenceName()`. Make sure to call this before the view is laid out for proper selector positioning.
