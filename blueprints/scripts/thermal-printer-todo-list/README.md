# TODO List Thermal Printer Blueprint

## 🚀 Installation

### Method 1: Import URL (Recommended)
1. Go to **Settings** → **Automations & Scenes** → **Blueprints**
2. Click **Import Blueprint**
3. Paste this URL: `https://github.com/yourusername/thermal-printer-blueprints/blob/main/todo_printer.yaml`
4. Click **Preview** and then **Import**

### Method 2: Manual Installation
1. Save the blueprint YAML to `/config/blueprints/script/todo_printer.yaml`
2. Restart Home Assistant
3. Go to **Settings** → **Automations & Scenes** → **Scripts**
4. Click **Add Script** → **Start with Blueprint**

## ⚙️ Configuration Options

### **Required Settings**
- **Thermal Printer Device**: Your ESPHome device name (e.g., `thermal_printer`)
- **Default TODO List**: Which TODO entity to use by default

### **Styling Options**
- **Header Style**: Simple, Decorated, or Minimal
- **Text Size**: Small, Medium, or Large
- **Checkbox Style**: `[ ]`, `☐ ☑`, or `- x`
- **Footer Info**: What to show at the bottom

### **Behavior Options**
- **Include Completed Items**: Show finished tasks
- **Add Date**: Include timestamp in header
- **Extra Spacing**: More space between items

## 📋 Usage Examples

### Basic Usage
```yaml
service: script.print_my_todo_list
```

### With Custom Parameters
```yaml
service: script.print_my_todo_list
data:
  todo_entity: "todo.grocery_list"
  include_completed: true
  add_date: false
```

### In Automations
```yaml
automation:
  - alias: "Print Shopping List When Leaving"
    trigger:
      - platform: state
        entity_id: person.john
        from: "home"
        to: "not_home"
    action:
      - service: script.print_my_todo_list
        data:
          todo_entity: "todo.shopping_list"
```

## 🎨 Output Examples

### Simple Header Style
```
SHOPPING LIST
================================
[ ] Milk
[ ] Bread
[X] Eggs
[ ] Cheese

3 items (1 completed)
2024-01-15 14:30
```

### Decorated Header Style
```
╔═══════════════════════════════╗
║        SHOPPING LIST          ║
╚═══════════════════════════════╝

☐ Milk
☐ Bread  
☑ Eggs
☐ Cheese

3 items (1 completed)
2024-01-15 14:30
```

### Minimal Style
```
- Milk
- Bread
x Eggs
- Cheese

3 items
```

## 🔧 Dashboard Integration

### Simple Button Card
```yaml
type: button
entity: script.print_my_todo_list
name: "Print Shopping List"
icon: mdi:printer-outline
tap_action:
  action: call-service
  service: script.print_my_todo_list
```

### Advanced Mushroom Card
```yaml
type: custom:mushroom-template-card
primary: "Print {{ state_attr('todo.shopping_list', 'friendly_name') }}"
secondary: >
  {{ state_attr('todo.shopping_list', 'items') 
     | selectattr('status', 'equalto', 'needs_action') 
     | list | length }} pending items
icon: mdi:format-list-checkbox
icon_color: >
  {% set count = state_attr('todo.shopping_list', 'items') 
                 | selectattr('status', 'equalto', 'needs_action') 
                 | list | length %}
  {% if count == 0 %}green
  {% elif count < 5 %}orange
  {% else %}red{% endif %}
tap_action:
  action: call-service
  service: script.print_my_todo_list
  service_data:
    todo_entity: "todo.shopping_list"
```

## 🗣️ Voice Assistant Integration

### With Assist
```yaml
automation:
  - alias: "Voice Print TODO List"
    trigger:
      - platform: conversation
        command: "Print my [shopping list|todo list|tasks]"
    action:
      - service: script.print_my_todo_list
        data:
          todo_entity: >
            {% if 'shopping' in trigger.text %}
              todo.shopping_list
            {% elif 'task' in trigger.text %}
              todo.tasks
            {% else %}
              todo.shopping_list
            {% endif %}
```

## 🛠️ Troubleshooting

### Common Issues

1. **"Entity not found" error**
   - Check your thermal printer device name in ESPHome
   - Ensure the device is online and responding

2. **"TODO entity not available"**
   - Verify your TODO list entity exists
   - Check the entity ID in Developer Tools

3. **Nothing prints**
   - Test thermal printer with: `service: esphome.DEVICE_test_print`
   - Check paper and power supply
   - Verify printer is awake

4. **Characters look wrong**
   - Adjust text size in blueprint configuration
   - Try different checkbox styles
   - Check printer font support

### Testing Commands

```yaml
# Test printer connectivity
service: esphome.thermal_printer_wake_printer

# Test basic printing
service: esphome.thermal_printer_print_text
data:
  message: "Blueprint Test"
  text_size: "M"
  alignment: "C"

# Check TODO entity
{{ state_attr('todo.shopping_list', 'items') }}
```

## 📱 Mobile App Integration

### iOS Shortcuts
1. Create new shortcut
2. Add "Get My Shortcuts" action  
3. Add "Call Service" action:
   - Service: `script.print_my_todo_list`
   - Data: `{"todo_entity": "todo.shopping_list"}`

### Android Tasker
1. Create new task
2. Add "HTTP Request" action
3. URL: `http://your-ha-url:8123/api/services/script/print_my_todo_list`
4. Headers: `Authorization: Bearer YOUR_TOKEN`

## 🔄 Blueprint Updates

The blueprint includes versioning and can be updated automatically:
1. Go to **Blueprints** in Home Assistant
2. Click the **Update** button when available
3. Review changes and click **Update Blueprint**

## 💡 Tips & Tricks

- **Priority Items**: Add `!` to item names for visual emphasis
- **Categories**: Use different TODO lists for different types (shopping, work, etc.)
- **Scheduling**: Set up automations to print daily/weekly lists
- **Multiple Printers**: Create separate scripts for different locations
- **Voice Control**: Use natural language with Assist integration

## 🤝 Contributing

Found a bug or want to suggest improvements? 
- Open an issue on GitHub
- Submit a pull request
- Share your automation examples

## 📄 License

This blueprint is released under the MIT License.
